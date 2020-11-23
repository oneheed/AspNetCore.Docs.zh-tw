---
title: RazorASP.NET Core 中的頁面簡介
author: Rick-Anderson
description: 說明 Razor ASP.NET Core 中的頁面如何讓撰寫以頁面為焦點的案例撰寫程式碼比使用 MVC 更簡單、更具生產力。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: bc5f3c85d7ace120dec8369f4550a097b4f258ce
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417639"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="90fa5-103">RazorASP.NET Core 中的頁面簡介</span><span class="sxs-lookup"><span data-stu-id="90fa5-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="90fa5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="90fa5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="90fa5-105">Razor 頁面可讓撰寫以頁面為焦點的案常式序代碼比使用控制器和視圖更簡單且更具生產力。</span><span class="sxs-lookup"><span data-stu-id="90fa5-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="90fa5-106">如果您在尋找使用模型檢視控制器方法的教學課程，請參閱[開始使用 ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="90fa5-107">本檔提供 Razor 頁面簡介。</span><span class="sxs-lookup"><span data-stu-id="90fa5-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="90fa5-108">它不是逐步教學課程。</span><span class="sxs-lookup"><span data-stu-id="90fa5-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="90fa5-109">如果您發現某些區段太過先進，請參閱 [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="90fa5-110">如需 ASP.NET Core 的概觀，請參閱[ASP.NET Core 簡介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="90fa5-111">必要條件</span><span class="sxs-lookup"><span data-stu-id="90fa5-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="90fa5-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90fa5-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="90fa5-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90fa5-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90fa5-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90fa5-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="90fa5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90fa5-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="90fa5-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90fa5-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90fa5-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90fa5-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="90fa5-118">建立 Razor 頁面專案</span><span class="sxs-lookup"><span data-stu-id="90fa5-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90fa5-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90fa5-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90fa5-120">如需如何建立頁面專案的詳細指示，請參閱 [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start) Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="90fa5-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90fa5-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="90fa5-122">從命令列執行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90fa5-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90fa5-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="90fa5-124">如需如何建立頁面專案的詳細指示，請參閱 [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start) Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="90fa5-125">Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="90fa5-125">Razor Pages</span></span>

<span data-ttu-id="90fa5-126">Razor 頁面已在 *Startup.cs* 中啟用：</span><span class="sxs-lookup"><span data-stu-id="90fa5-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="90fa5-127">請考慮使用基本頁面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="90fa5-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="90fa5-128">上述程式碼看起來很像是在具有控制器和 views 的 ASP.NET Core 應用程式中使用的[ Razor 視圖](xref:tutorials/first-mvc-app/adding-view)檔案。</span><span class="sxs-lookup"><span data-stu-id="90fa5-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="90fa5-129">這會讓它不同的是指示詞 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="90fa5-130">`@page` 會將檔案轉換成 MVC 動作，這表示它會直接處理要求，不用透過控制器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="90fa5-131">`@page` 必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="90fa5-132">`@page` 會影響其他結構的行為 [Razor](xref:mvc/views/razor) 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="90fa5-133">Razor 分頁檔名的名稱必須是 *cshtml* 尾碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="90fa5-134">使用`PageModel`類別的類似頁面，顯示於下列兩個檔案中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="90fa5-135">*Pages/Index2.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="90fa5-136">*Pages/Index2.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="90fa5-137">依照慣例，類別檔案的名稱會與 `PageModel` Razor 附加 *.cs* 的分頁檔相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="90fa5-138">例如，前一 Razor 頁是 *Pages/index2.cshtml.cs。*</span><span class="sxs-lookup"><span data-stu-id="90fa5-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="90fa5-139">包含 `PageModel` 類別的檔案名為 *Pages/Index2.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="90fa5-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="90fa5-140">頁面的 URL 路徑關聯是由頁面在檔案系統中的位置決定。</span><span class="sxs-lookup"><span data-stu-id="90fa5-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="90fa5-141">下表顯示 Razor 頁面路徑和相符的 URL：</span><span class="sxs-lookup"><span data-stu-id="90fa5-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="90fa5-142">檔案名稱和路徑</span><span class="sxs-lookup"><span data-stu-id="90fa5-142">File name and path</span></span>               | <span data-ttu-id="90fa5-143">比對 URL</span><span class="sxs-lookup"><span data-stu-id="90fa5-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="90fa5-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="90fa5-145">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="90fa5-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="90fa5-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="90fa5-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="90fa5-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="90fa5-149">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="90fa5-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="90fa5-150">注意：</span><span class="sxs-lookup"><span data-stu-id="90fa5-150">Notes:</span></span>

* <span data-ttu-id="90fa5-151">執行時間預設會 Razor 在 [ *pages* ] 資料夾中尋找分頁檔。</span><span class="sxs-lookup"><span data-stu-id="90fa5-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="90fa5-152">`Index` 是 URL 未包含頁面時的預設頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="90fa5-153">撰寫基本表單</span><span class="sxs-lookup"><span data-stu-id="90fa5-153">Write a basic form</span></span>

<span data-ttu-id="90fa5-154">Razor 頁面的設計目的是要讓搭配網頁瀏覽器使用的常見模式在建立應用程式時很容易執行。</span><span class="sxs-lookup"><span data-stu-id="90fa5-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="90fa5-155">[模型](xref:mvc/models/model-binding)系結 [、卷](xref:mvc/views/tag-helpers/intro)標協助程式和 HTML 協助程式全都 *是* 使用頁面類別中定義的屬性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="90fa5-156">`Contact` 模型請考慮實作基本的「與我們連絡」格式頁面：</span><span class="sxs-lookup"><span data-stu-id="90fa5-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="90fa5-157">本文件中的範例，會在 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) 檔案中初始化 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="90fa5-158">記憶體中的資料庫需要 `Microsoft.EntityFrameworkCore.InMemory` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="90fa5-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="90fa5-159">資料模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="90fa5-160">DB 內容：</span><span class="sxs-lookup"><span data-stu-id="90fa5-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="90fa5-161">*Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="90fa5-162">*Pages/Create.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="90fa5-163">依照慣例，`PageModel` 類別稱之為 `<PageName>Model`，與頁面位於相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="90fa5-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="90fa5-164">`PageModel` 類別可以分離頁面邏輯與頁面展示。</span><span class="sxs-lookup"><span data-stu-id="90fa5-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="90fa5-165">此類別會定義頁面的處理常式，以處理傳送至頁面的要求與用於轉譯頁面的資料。</span><span class="sxs-lookup"><span data-stu-id="90fa5-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="90fa5-166">這種分隔允許：</span><span class="sxs-lookup"><span data-stu-id="90fa5-166">This separation allows:</span></span>

* <span data-ttu-id="90fa5-167">透過相依性 [插入](xref:fundamentals/dependency-injection)來管理頁面相依性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="90fa5-168">單元測試</span><span class="sxs-lookup"><span data-stu-id="90fa5-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="90fa5-169">在 `POST` 要求上執行的頁面具有 「處理常式方法」`OnPostAsync`  (當使用者張貼表單時)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="90fa5-170">可以新增任何 HTTP 指令動詞的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="90fa5-171">最常見的處理常式包括：</span><span class="sxs-lookup"><span data-stu-id="90fa5-171">The most common handlers are:</span></span>

* <span data-ttu-id="90fa5-172">`OnGet`，初始化頁所需要的狀態。</span><span class="sxs-lookup"><span data-stu-id="90fa5-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="90fa5-173">在上述程式碼中，此 `OnGet` 方法會顯示 *CreateModel 的 cshtml* Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="90fa5-174">`OnPost`，處理表單提交作業。</span><span class="sxs-lookup"><span data-stu-id="90fa5-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="90fa5-175">`Async` 命名尾碼是選擇性的，但依照慣例通常用於非同步函式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="90fa5-176">上述程式碼一般適用于 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="90fa5-177">如果您熟悉使用控制器和 views 的 ASP.NET apps：</span><span class="sxs-lookup"><span data-stu-id="90fa5-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="90fa5-178">`OnPostAsync`上述範例中的程式碼看起來類似一般的控制器程式碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="90fa5-179">大部分的 MVC 基本專案，例如 [模型](xref:mvc/models/model-binding)系結、 [驗證](xref:mvc/models/validation)和動作結果，其運作方式與控制器和 Razor 頁面相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="90fa5-180">前一個 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="90fa5-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="90fa5-181">`OnPostAsync` 的基本流程：</span><span class="sxs-lookup"><span data-stu-id="90fa5-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="90fa5-182">檢查驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="90fa5-182">Check for validation errors.</span></span>

* <span data-ttu-id="90fa5-183">如果沒有任何錯誤，會儲存資料並重新導向。</span><span class="sxs-lookup"><span data-stu-id="90fa5-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="90fa5-184">如果有錯誤，會再次顯示有驗證訊息的頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="90fa5-185">在許多情況下，會在用戶端上偵測到驗證錯誤，且永遠不會提交到伺服器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="90fa5-186">*Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="90fa5-187">從 *Pages/Create. cshtml* 轉譯的 HTML：</span><span class="sxs-lookup"><span data-stu-id="90fa5-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="90fa5-188">在先前的程式碼中，張貼表單：</span><span class="sxs-lookup"><span data-stu-id="90fa5-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="90fa5-189">包含有效資料：</span><span class="sxs-lookup"><span data-stu-id="90fa5-189">With valid data:</span></span>

  * <span data-ttu-id="90fa5-190">`OnPostAsync`處理常式方法會呼叫 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper 方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="90fa5-191">`RedirectToPage` 會傳回 <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="90fa5-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="90fa5-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="90fa5-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="90fa5-193">是動作結果。</span><span class="sxs-lookup"><span data-stu-id="90fa5-193">Is an action result.</span></span>
    * <span data-ttu-id="90fa5-194">類似于 `RedirectToAction` 或 `RedirectToRoute` (用於控制器和 views) 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="90fa5-195">已針對頁面自訂。</span><span class="sxs-lookup"><span data-stu-id="90fa5-195">Is customized for pages.</span></span> <span data-ttu-id="90fa5-196">在上述範例中，它會重新導向至根索引頁面 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="90fa5-197">[產生頁面 URL](#url_gen)一節會詳細說明 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="90fa5-198">傳遞至伺服器的驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="90fa5-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="90fa5-199">`OnPostAsync`處理常式方法會呼叫 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper 方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="90fa5-200">`Page` 會傳回 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="90fa5-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="90fa5-201">傳回 `Page` 類似於控制站中的動作傳回 `View`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="90fa5-202">`PageResult` 這是處理常式方法的預設傳回型別。</span><span class="sxs-lookup"><span data-stu-id="90fa5-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="90fa5-203">傳回 `void` 的處理常式方法會呈現頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="90fa5-204">在上述範例中，不使用任何值張貼表單會導致 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 傳回 false。</span><span class="sxs-lookup"><span data-stu-id="90fa5-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="90fa5-205">在此範例中，用戶端上不會顯示任何驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="90fa5-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="90fa5-206">本檔稍後會討論驗證錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="90fa5-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="90fa5-207">使用用戶端驗證偵測到驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="90fa5-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="90fa5-208">資料 **不** 會張貼至伺服器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="90fa5-209">本檔稍後會說明用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="90fa5-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="90fa5-210">`Customer`屬性使用 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 屬性來加入宣告模型系結：</span><span class="sxs-lookup"><span data-stu-id="90fa5-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="90fa5-211">`[BindProperty]`**不** 應該用於包含不應由用戶端變更之屬性的模型。</span><span class="sxs-lookup"><span data-stu-id="90fa5-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="90fa5-212">如需詳細資訊，請參閱 [大量指派](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="90fa5-213">Razor 依預設，頁面只會系結具有非動詞命令的屬性 `GET` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="90fa5-214">系結至屬性不需要撰寫程式碼，就能將 HTTP 資料轉換成模型類型。</span><span class="sxs-lookup"><span data-stu-id="90fa5-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="90fa5-215">透過使用相同的屬性呈現表單欄位 (`<input asp-for="Customer.Name">`) 並接受輸入，繫結可以減少程式碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="90fa5-216">檢查 *Pages/Create. cshtml* view 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="90fa5-217">在上述程式碼中， [輸入標記](xref:mvc/views/working-with-forms#the-input-tag-helper)協助程式會將 `<input asp-for="Customer.Name" />` HTML 元素系結 `<input>` 至 `Customer.Name` 模型運算式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="90fa5-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) 讓標籤協助程式可供使用。</span><span class="sxs-lookup"><span data-stu-id="90fa5-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="90fa5-219">首頁</span><span class="sxs-lookup"><span data-stu-id="90fa5-219">The home page</span></span>

<span data-ttu-id="90fa5-220">*Index. cshtml* 是首頁：</span><span class="sxs-lookup"><span data-stu-id="90fa5-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="90fa5-221">已建立關聯的 `PageModel` 類別 (*Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="90fa5-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="90fa5-222">此 *索引的 cshtml* 檔案包含下列標記：</span><span class="sxs-lookup"><span data-stu-id="90fa5-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="90fa5-223">`<a /a>`[錨點](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)標籤協助程式使用 `asp-route-{value}` 屬性來產生編輯頁面的連結。</span><span class="sxs-lookup"><span data-stu-id="90fa5-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="90fa5-224">該連結包含路由資料和連絡人識別碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="90fa5-225">例如： `https://localhost:5001/Edit/1` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="90fa5-226">[標記](xref:mvc/views/tag-helpers/intro) 協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="90fa5-227">此 *索引的 cshtml* 檔案包含標記，以建立每個客戶連絡人的 [刪除] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="90fa5-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="90fa5-228">呈現的 HTML：</span><span class="sxs-lookup"><span data-stu-id="90fa5-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="90fa5-229">在 HTML 中轉譯 [刪除] 按鈕時，其 [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) 包含下列參數：</span><span class="sxs-lookup"><span data-stu-id="90fa5-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="90fa5-230">由屬性指定的客戶連絡人識別碼 `asp-route-id` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="90fa5-231">`handler`屬性所指定的 `asp-page-handler` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="90fa5-232">選取按鈕時，表單 `POST` 要求會傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="90fa5-233">依照慣例，會依據配置 `OnPost[handler]Async`，按 `handler` 參數的值來選取處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="90fa5-234">在此範例中，因為 `handler` 為 `delete`，所以會使用 `OnPostDeleteAsync` 處理常式方法來處理 `POST` 要求。</span><span class="sxs-lookup"><span data-stu-id="90fa5-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="90fa5-235">若 `asp-page-handler` 設為其他值 (例如 `remove`)，則會選取名為 `OnPostRemoveAsync` 的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="90fa5-236">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="90fa5-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="90fa5-237">`id`從查詢字串取得。</span><span class="sxs-lookup"><span data-stu-id="90fa5-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="90fa5-238">使用 `FindAsync` 在資料庫中查詢客戶連絡人。</span><span class="sxs-lookup"><span data-stu-id="90fa5-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="90fa5-239">如果找到客戶連絡人，則會將它移除，並更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="90fa5-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="90fa5-240">呼叫 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> 以重新導向至根索引頁 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="90fa5-241">編輯 cshtml 檔案</span><span class="sxs-lookup"><span data-stu-id="90fa5-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="90fa5-242">第一行包含 `@page "{id:int}"` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="90fa5-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="90fa5-243">路由條件約束 `"{id:int}"` 通知頁面接受包含 `int` 路由資料的頁面要求。</span><span class="sxs-lookup"><span data-stu-id="90fa5-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="90fa5-244">如果頁面要求不包含可以轉換成 `int` 的路由資料，執行階段會傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="90fa5-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="90fa5-245">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="90fa5-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="90fa5-246">*Edit.cshtml.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="90fa5-247">驗證</span><span class="sxs-lookup"><span data-stu-id="90fa5-247">Validation</span></span>

<span data-ttu-id="90fa5-248">驗證規則：</span><span class="sxs-lookup"><span data-stu-id="90fa5-248">Validation rules:</span></span>

* <span data-ttu-id="90fa5-249">在模型類別中會以宣告方式指定。</span><span class="sxs-lookup"><span data-stu-id="90fa5-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="90fa5-250">會在應用程式的任何位置強制執行。</span><span class="sxs-lookup"><span data-stu-id="90fa5-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="90fa5-251"><xref:System.ComponentModel.DataAnnotations>命名空間會提供一組內建的驗證屬性，這些屬性會以宣告方式套用至類別或屬性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="90fa5-252">DataAnnotations 也包含格式化屬性 [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) ，例如格式化的說明，而且不提供任何驗證。</span><span class="sxs-lookup"><span data-stu-id="90fa5-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="90fa5-253">請考慮 `Customer` 模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="90fa5-254">使用下列 *Create. cshtml* view 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="90fa5-255">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="90fa5-255">The preceding code:</span></span>

* <span data-ttu-id="90fa5-256">包含 jQuery 和 jQuery 驗證腳本。</span><span class="sxs-lookup"><span data-stu-id="90fa5-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="90fa5-257">使用 `<div />` 和 `<span />` [標記](xref:mvc/views/tag-helpers/intro) 協助程式來啟用：</span><span class="sxs-lookup"><span data-stu-id="90fa5-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="90fa5-258">用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="90fa5-258">Client-side validation.</span></span>
  * <span data-ttu-id="90fa5-259">轉譯時發生驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="90fa5-259">Validation error rendering.</span></span>

* <span data-ttu-id="90fa5-260">產生下列 HTML：</span><span class="sxs-lookup"><span data-stu-id="90fa5-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="90fa5-261">張貼沒有名稱值的建立表單時，會顯示錯誤訊息：「需要名稱欄位」。</span><span class="sxs-lookup"><span data-stu-id="90fa5-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="90fa5-262">在表單上。</span><span class="sxs-lookup"><span data-stu-id="90fa5-262">on the form.</span></span> <span data-ttu-id="90fa5-263">如果在用戶端上啟用 JavaScript，則瀏覽器會顯示錯誤，而不會張貼至伺服器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="90fa5-264">`[StringLength(10)]`屬性會 `data-val-length-max="10"` 在呈現的 HTML 上產生。</span><span class="sxs-lookup"><span data-stu-id="90fa5-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="90fa5-265">`data-val-length-max` 防止瀏覽器輸入超過指定的最大長度。</span><span class="sxs-lookup"><span data-stu-id="90fa5-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="90fa5-266">如果使用 [Fiddler](https://www.telerik.com/fiddler) 之類的工具來編輯和重新播放 post：</span><span class="sxs-lookup"><span data-stu-id="90fa5-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="90fa5-267">名稱超過10。</span><span class="sxs-lookup"><span data-stu-id="90fa5-267">With the name longer than 10.</span></span>
* <span data-ttu-id="90fa5-268">錯誤訊息「功能變數名稱必須是最大長度為10的字串」。</span><span class="sxs-lookup"><span data-stu-id="90fa5-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="90fa5-269">」錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="90fa5-269">is returned.</span></span>

<span data-ttu-id="90fa5-270">請考慮下列 `Movie` 模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="90fa5-271">驗證屬性會指定在其套用的模型屬性上強制執行的行為：</span><span class="sxs-lookup"><span data-stu-id="90fa5-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="90fa5-272">`Required`和 `MinimumLength` 屬性工作表示屬性必須有值，但不會防止使用者輸入空白字元來滿足這種驗證。</span><span class="sxs-lookup"><span data-stu-id="90fa5-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="90fa5-273">`RegularExpression` 屬性則用來限制可輸入的字元。</span><span class="sxs-lookup"><span data-stu-id="90fa5-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="90fa5-274">在上述程式碼中，"Genre"：</span><span class="sxs-lookup"><span data-stu-id="90fa5-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="90fa5-275">必須指使用字母。</span><span class="sxs-lookup"><span data-stu-id="90fa5-275">Must only use letters.</span></span>
  * <span data-ttu-id="90fa5-276">第一個字母必須是大寫。</span><span class="sxs-lookup"><span data-stu-id="90fa5-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="90fa5-277">不允許使用空格、數字和特殊字元。</span><span class="sxs-lookup"><span data-stu-id="90fa5-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="90fa5-278">`RegularExpression` "Rating"：</span><span class="sxs-lookup"><span data-stu-id="90fa5-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="90fa5-279">第一個字元必須為大寫字母。</span><span class="sxs-lookup"><span data-stu-id="90fa5-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="90fa5-280">允許後續空格中的特殊字元和數位。</span><span class="sxs-lookup"><span data-stu-id="90fa5-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="90fa5-281">"PG-13" 對分級而言有效，但不適用於 "Genre"。</span><span class="sxs-lookup"><span data-stu-id="90fa5-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="90fa5-282">`Range` 屬性會將值限制在指定的範圍內。</span><span class="sxs-lookup"><span data-stu-id="90fa5-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="90fa5-283">`StringLength`屬性會設定字串屬性的最大長度，並選擇性地設定其最小長度。</span><span class="sxs-lookup"><span data-stu-id="90fa5-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="90fa5-284">實值型別 (如`decimal`、`int`、`float`、`DateTime`) 原本就是必要項目，而且不需要 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="90fa5-285">模型的 [建立] 頁面會顯示 `Movie` 具有無效值的錯誤：</span><span class="sxs-lookup"><span data-stu-id="90fa5-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![有多個 jQuery 用戶端驗證錯誤的電影檢視表單](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="90fa5-287">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="90fa5-287">For more information, see:</span></span>

* [<span data-ttu-id="90fa5-288">將驗證新增至電影應用程式</span><span class="sxs-lookup"><span data-stu-id="90fa5-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="90fa5-289">[ASP.NET Core 中的模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="90fa5-290">使用 OnGet 處理常式後援來處理 HEAD 要求</span><span class="sxs-lookup"><span data-stu-id="90fa5-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="90fa5-291">`HEAD` 要求可讓您取得特定資源的標頭。</span><span class="sxs-lookup"><span data-stu-id="90fa5-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="90fa5-292">不同於 `GET` 要求，`HEAD` 要求不會傳回回應主體。</span><span class="sxs-lookup"><span data-stu-id="90fa5-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="90fa5-293">一般來說，會為 `HEAD` 要求建立及呼叫 `OnHead` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="90fa5-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="90fa5-294">Razor`OnGet`如果未 `OnHead` 定義任何處理程式，則頁面會切換回呼叫處理常式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="90fa5-295">XSRF/CSRF 和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="90fa5-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="90fa5-296">Razor 頁面會受到 [Antiforgery 驗證](xref:security/anti-request-forgery)的保護。</span><span class="sxs-lookup"><span data-stu-id="90fa5-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="90fa5-297">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)會將 antiforgery token 插入至 HTML 表單元素。</span><span class="sxs-lookup"><span data-stu-id="90fa5-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="90fa5-298">使用頁面的版面配置、部分、範本和標記協助程式 Razor</span><span class="sxs-lookup"><span data-stu-id="90fa5-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="90fa5-299">頁面會使用 view engine 的所有功能 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="90fa5-300">配置、部分、範本、標籤協助程式、 *_ViewStart cshtml* 和 *_ViewImports. cshtml* 的運作方式與傳統視圖的運作方式相同。 Razor</span><span class="sxs-lookup"><span data-stu-id="90fa5-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="90fa5-301">可利用這些功能的一部分來整理這個頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="90fa5-302">將 [版面配置頁面](xref:mvc/views/layout)新增至 *Pages/Shared/_Layout.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="90fa5-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="90fa5-303">[版面](xref:mvc/views/layout)配置：</span><span class="sxs-lookup"><span data-stu-id="90fa5-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="90fa5-304">控制每個頁面的版面配置 (除非頁面退出版面配置)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="90fa5-305">匯入 HTML 結構，例如 JavaScript 和樣式表。</span><span class="sxs-lookup"><span data-stu-id="90fa5-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="90fa5-306">頁面的內容 Razor 會轉譯 `@RenderBody()` 為呼叫的位置。</span><span class="sxs-lookup"><span data-stu-id="90fa5-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="90fa5-307">如需詳細資訊，請參閱 [版面配置頁面](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="90fa5-308">[版面配置](xref:mvc/views/layout#specifying-a-layout)屬性是在 *Pages/_ViewStart.cshtml* 中設定：</span><span class="sxs-lookup"><span data-stu-id="90fa5-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="90fa5-309">版面配置位於 *Pages/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="90fa5-310">頁面會以階層方式尋找其他檢視 (版面配置、範本、部分)，從目前頁面的相同資料夾開始。</span><span class="sxs-lookup"><span data-stu-id="90fa5-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="90fa5-311">*頁面/共用* 資料夾中的版面配置可以從 Razor [ *pages* ] 資料夾下的任何頁面使用。</span><span class="sxs-lookup"><span data-stu-id="90fa5-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="90fa5-312">版面配置頁面應位於 *Pages/Shared* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="90fa5-313">我們 **不** 建議您將配置檔案放入 *Views/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="90fa5-314">*Views/Shared* 是 MVC 檢視模式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="90fa5-315">Razor 頁面的目的是要依賴資料夾階層，不是路徑慣例。</span><span class="sxs-lookup"><span data-stu-id="90fa5-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="90fa5-316">頁面上的視圖搜尋 Razor 包含 *Pages* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="90fa5-317">適用于 MVC 控制器和傳統視圖的版面配置、範本和 Razor 部分 *只會運作*。</span><span class="sxs-lookup"><span data-stu-id="90fa5-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="90fa5-318">新增 *Pages/_ViewImports.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="90fa5-319">本教學課程稍後會說明 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="90fa5-320">`@addTagHelper` 指示詞會將 [內建標記協助程式](xref:mvc/views/tag-helpers/builtin-th/Index)帶入 *Pages* 資料夾中的所有頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="90fa5-321">在 `@namespace` 頁面上設定的指示詞：</span><span class="sxs-lookup"><span data-stu-id="90fa5-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="90fa5-322">指示詞會 `@namespace` 設定頁面的命名空間。</span><span class="sxs-lookup"><span data-stu-id="90fa5-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="90fa5-323">`@model` 指示詞不需要包含命名空間。</span><span class="sxs-lookup"><span data-stu-id="90fa5-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="90fa5-324">當 `@namespace` 指示詞包含在 *_ViewImports.cshtml* 中時，指定的命名空間會在匯入 `@namespace` 指示詞的頁面中提供所產生之命名空間的前置詞。</span><span class="sxs-lookup"><span data-stu-id="90fa5-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="90fa5-325">所產生命名空間的其餘部分 (後置字元部分) 是包含 *_ViewImports.cshtml* 的資料夾和包含頁面的資料夾之間，以句點分隔的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="90fa5-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="90fa5-326">例如，`PageModel` 類別 *Pages/Customers/Edit.cshtml.cs* 會明確地設定命名空間：</span><span class="sxs-lookup"><span data-stu-id="90fa5-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="90fa5-327">*Pages/_ViewImports.cshtml* 檔案會設定下列命名空間：</span><span class="sxs-lookup"><span data-stu-id="90fa5-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="90fa5-328">針對 *Pages/Customers/Edit. cshtml* 頁面產生的命名空間與 Razor `PageModel` 類別相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="90fa5-329">`@namespace`*也可搭配傳統 Razor 視圖使用。*</span><span class="sxs-lookup"><span data-stu-id="90fa5-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="90fa5-330">請考慮 *Pages/Create. cshtml* view 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="90fa5-331">更新的 *Pages/Create. cshtml* view 檔案，其中包含 *_ViewImports. cshtml* 和先前的版面配置檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="90fa5-332">在上述程式碼中， *_ViewImports 的 cshtml* 已匯入命名空間和標記協助程式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="90fa5-333">設定檔案匯入 JavaScript 檔案。</span><span class="sxs-lookup"><span data-stu-id="90fa5-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="90fa5-334">[ Razor Pages 入門專案](#rpvs17)包含 *pages/_ValidationScriptsPartial. cshtml*，可連結用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="90fa5-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="90fa5-335">如需部分檢視的詳細資訊，請參閱 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="90fa5-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="90fa5-336">產生頁面 URL</span><span class="sxs-lookup"><span data-stu-id="90fa5-336">URL generation for Pages</span></span>

<span data-ttu-id="90fa5-337">前面出現過的 `Create` 頁面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="90fa5-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="90fa5-338">應用程式有下列檔案/資料夾結構：</span><span class="sxs-lookup"><span data-stu-id="90fa5-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="90fa5-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="90fa5-339">*/Pages*</span></span>

  * <span data-ttu-id="90fa5-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="90fa5-341">*隱私權. cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="90fa5-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="90fa5-342">*/Customers*</span></span>

    * <span data-ttu-id="90fa5-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="90fa5-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="90fa5-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-345">*Index.cshtml*</span></span>

<span data-ttu-id="90fa5-346">*Pages/customers/Create. cshtml* 和 *Pages/customers/Edit. cshtml* 頁面會在成功之後重新導向至 *Pages/customers/Index。 cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="90fa5-347">字串 `./Index` 是用來存取前一個頁面的相對頁面名稱。</span><span class="sxs-lookup"><span data-stu-id="90fa5-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="90fa5-348">它是用來產生 *Pages/Customers/Index. cshtml* 頁面的 url。</span><span class="sxs-lookup"><span data-stu-id="90fa5-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="90fa5-349">例如：</span><span class="sxs-lookup"><span data-stu-id="90fa5-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="90fa5-350">絕對頁面名稱 `/Index` 是用來產生 *Pages/Index. cshtml* 頁面的 url。</span><span class="sxs-lookup"><span data-stu-id="90fa5-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="90fa5-351">例如：</span><span class="sxs-lookup"><span data-stu-id="90fa5-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="90fa5-352">頁面名稱是從根 */Pages* 資料夾到該頁面的路徑 (包括前置的 `/`，例如 `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="90fa5-353">上述的 URL 產生範例提供增強的選項和功能功能，而不是硬式編碼 URL。</span><span class="sxs-lookup"><span data-stu-id="90fa5-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="90fa5-354">URL 產生使用[路由](xref:mvc/controllers/routing)，可以根據路由在目的地路徑中定義的方式，產生並且編碼參數。</span><span class="sxs-lookup"><span data-stu-id="90fa5-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="90fa5-355">產生頁面 URL 支援相關的名稱。</span><span class="sxs-lookup"><span data-stu-id="90fa5-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="90fa5-356">下表顯示使用 `RedirectToPage` *Pages/Customers/Create. cshtml* 中的不同參數所選取的索引頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="90fa5-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="90fa5-357">RedirectToPage(x)</span></span>| <span data-ttu-id="90fa5-358">頁面</span><span class="sxs-lookup"><span data-stu-id="90fa5-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="90fa5-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="90fa5-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="90fa5-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-360">*Pages/Index*</span></span> |
| <span data-ttu-id="90fa5-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="90fa5-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="90fa5-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="90fa5-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="90fa5-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="90fa5-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-364">*Pages/Index*</span></span> |
| <span data-ttu-id="90fa5-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="90fa5-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="90fa5-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="90fa5-367">`RedirectToPage("Index")`、 `RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是 *相對名稱*。</span><span class="sxs-lookup"><span data-stu-id="90fa5-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="90fa5-368">`RedirectToPage` 參數「結合」了目前頁面的路徑，以計算目的地頁面的名稱。</span><span class="sxs-lookup"><span data-stu-id="90fa5-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="90fa5-369">相對名稱連結在以複雜結構建置網站時很有用。</span><span class="sxs-lookup"><span data-stu-id="90fa5-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="90fa5-370">當使用相對名稱連結資料夾中的頁面時：</span><span class="sxs-lookup"><span data-stu-id="90fa5-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="90fa5-371">重新命名資料夾並不會中斷相對連結。</span><span class="sxs-lookup"><span data-stu-id="90fa5-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="90fa5-372">連結不會中斷，因為它們不包含資料夾名稱。</span><span class="sxs-lookup"><span data-stu-id="90fa5-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="90fa5-373">若要重新導向到不同[區域](xref:mvc/controllers/areas)中的頁面，請指定區域：</span><span class="sxs-lookup"><span data-stu-id="90fa5-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="90fa5-374">如需詳細資訊，請參閱 <xref:mvc/controllers/areas> 與 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="90fa5-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="90fa5-375">ViewData 屬性</span><span class="sxs-lookup"><span data-stu-id="90fa5-375">ViewData attribute</span></span>

<span data-ttu-id="90fa5-376">您可以使用將資料傳遞至頁面 <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="90fa5-377">具有屬性的屬性（property） `[ViewData]` 會從儲存和載入其值 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="90fa5-378">在下列範例中，會將 `AboutModel` `[ViewData]` 屬性套用至 `Title` 屬性：</span><span class="sxs-lookup"><span data-stu-id="90fa5-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="90fa5-379">在 [關於] 頁面上，存取 `Title` 屬性作為模型屬性：</span><span class="sxs-lookup"><span data-stu-id="90fa5-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="90fa5-380">在此配置中，標題會從 ViewData 字典中讀取：</span><span class="sxs-lookup"><span data-stu-id="90fa5-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="90fa5-381">TempData</span><span class="sxs-lookup"><span data-stu-id="90fa5-381">TempData</span></span>

<span data-ttu-id="90fa5-382">ASP.NET Core 會公開 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="90fa5-383">這個屬性會儲存資料，直到讀取為止。</span><span class="sxs-lookup"><span data-stu-id="90fa5-383">This property stores data until it's read.</span></span> <span data-ttu-id="90fa5-384"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> 和 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> 方法可以用來檢查資料，不用刪除。</span><span class="sxs-lookup"><span data-stu-id="90fa5-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="90fa5-385">`TempData` 當需要多個單一要求的資料時，適用于重新導向。</span><span class="sxs-lookup"><span data-stu-id="90fa5-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="90fa5-386">下列程式碼會設定使用 `TempData` 的 `Message` 值：</span><span class="sxs-lookup"><span data-stu-id="90fa5-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="90fa5-387">*Pages/Customers/Index.cshtml* 檔案中的下列標記會顯示使用 `TempData` 的 `Message` 值。</span><span class="sxs-lookup"><span data-stu-id="90fa5-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="90fa5-388">*Pages/Customers/Index.cshtml.cs* 頁面模型會將 `[TempData]` 屬性 (attribute) 套用到 `Message` 屬性 (property)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="90fa5-389">如需詳細資訊，請參閱 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="90fa5-390">每頁面有多個處理常式</span><span class="sxs-lookup"><span data-stu-id="90fa5-390">Multiple handlers per page</span></span>

<span data-ttu-id="90fa5-391">下列頁面會使用 `asp-page-handler` 標記協助程式為兩個處理常式產生標記：</span><span class="sxs-lookup"><span data-stu-id="90fa5-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="90fa5-392">上例中的表單有兩個提交按鈕，每一個都使用 `FormActionTagHelper` 提交至不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="90fa5-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="90fa5-393">`asp-page-handler` 屬性附隨於 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="90fa5-394">`asp-page-handler` 產生的 URL 會提交至頁面所定義的每一個處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="90fa5-395">因為範例連結至目前的頁面，所以未指定 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="90fa5-396">頁面模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="90fa5-397">上述程式碼使用「具名的處理常式方法」。</span><span class="sxs-lookup"><span data-stu-id="90fa5-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="90fa5-398">具名的處理常式方法的建立方式是採用名稱中在 `On<HTTP Verb>` 後面、`Async` 之前 (如有) 的文字。</span><span class="sxs-lookup"><span data-stu-id="90fa5-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="90fa5-399">在上例中，頁面方法是 OnPost **JoinList** Async 和 OnPost **JoinListUC** Async。</span><span class="sxs-lookup"><span data-stu-id="90fa5-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="90fa5-400">移除 *OnPost* 和 *Async*，處理常式名稱就是 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="90fa5-401">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="90fa5-402">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="90fa5-403">自訂路由</span><span class="sxs-lookup"><span data-stu-id="90fa5-403">Custom routes</span></span>

<span data-ttu-id="90fa5-404">使用 `@page` 指示詞，可以：</span><span class="sxs-lookup"><span data-stu-id="90fa5-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="90fa5-405">指定頁面的自訂路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-405">Specify a custom route to a page.</span></span> <span data-ttu-id="90fa5-406">例如，[關於] 頁面的路由可使用 `@page "/Some/Other/Path"` 設為 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="90fa5-407">將區段附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-407">Append segments to a page's default route.</span></span> <span data-ttu-id="90fa5-408">例如，使用 `@page "item"` 可將 "item" 區段新增到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="90fa5-409">將參數附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="90fa5-410">例如，具有 `@page "{id}"` 的頁面可要求識別碼參數 `id`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="90fa5-411">支援在路徑開頭以波狀符號 (`~`) 指定根相對路徑。</span><span class="sxs-lookup"><span data-stu-id="90fa5-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="90fa5-412">例如，`@page "~/Some/Other/Path"` 與 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="90fa5-413">如果您不喜歡 URL 中的查詢字串 `?handler=JoinList` ，請變更路由，將處理常式名稱放在 url 的路徑部分。</span><span class="sxs-lookup"><span data-stu-id="90fa5-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="90fa5-414">您可以藉由新增以雙引號括住的路由範本來自訂路由 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="90fa5-415">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="90fa5-416">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="90fa5-417">跟在 `handler` 後面的 `?` 表示路由參數為選擇性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="90fa5-418">Advanced configuration and settings</span><span class="sxs-lookup"><span data-stu-id="90fa5-418">Advanced configuration and settings</span></span>

<span data-ttu-id="90fa5-419">大部分的應用程式都不需要下列各節中的設定和設定。</span><span class="sxs-lookup"><span data-stu-id="90fa5-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="90fa5-420">若要設定 advanced 選項，請使用設定的多載 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> ：</span><span class="sxs-lookup"><span data-stu-id="90fa5-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="90fa5-421">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> 設定頁面的根目錄，或新增頁面的應用程式模型慣例。</span><span class="sxs-lookup"><span data-stu-id="90fa5-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="90fa5-422">如需慣例的詳細資訊，請參閱[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="90fa5-423">若要先行編譯視圖，請參閱[ Razor view 編譯](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="90fa5-424">指定 Razor 頁面位於內容根目錄</span><span class="sxs-lookup"><span data-stu-id="90fa5-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="90fa5-425">根據預設， Razor 頁面會根目錄在 */Pages* 目錄中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="90fa5-426">加入 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> ，以指定您的 Razor 頁面位於應用程式的 [內容根目錄](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) ：</span><span class="sxs-lookup"><span data-stu-id="90fa5-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="90fa5-427">指定 Razor 頁面位於自訂根目錄</span><span class="sxs-lookup"><span data-stu-id="90fa5-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="90fa5-428">新增 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> 以指定 Razor 頁面位於應用程式的自訂根目錄， (提供相對路徑) ：</span><span class="sxs-lookup"><span data-stu-id="90fa5-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="90fa5-429">其他資源</span><span class="sxs-lookup"><span data-stu-id="90fa5-429">Additional resources</span></span>

* <span data-ttu-id="90fa5-430">請參閱這篇簡介中的 [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="90fa5-431">授權屬性和 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="90fa5-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="90fa5-432">下載或查看範例程式碼</span><span class="sxs-lookup"><span data-stu-id="90fa5-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="90fa5-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90fa5-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="90fa5-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90fa5-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90fa5-435">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90fa5-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="90fa5-436">建立 Razor 頁面專案</span><span class="sxs-lookup"><span data-stu-id="90fa5-436">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90fa5-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90fa5-437">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90fa5-438">如需如何建立頁面專案的詳細指示，請參閱 [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start) Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-438">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90fa5-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90fa5-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="90fa5-440">從命令列執行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-440">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="90fa5-441">從 Visual Studio for Mac 開啟已產生的 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="90fa5-441">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="90fa5-442">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90fa5-442">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="90fa5-443">從命令列執行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-443">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="90fa5-444">Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="90fa5-444">Razor Pages</span></span>

<span data-ttu-id="90fa5-445">Razor 頁面已在 *Startup.cs* 中啟用：</span><span class="sxs-lookup"><span data-stu-id="90fa5-445">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="90fa5-446">請考慮使用基本頁面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="90fa5-446">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="90fa5-447">上述程式碼看起來很像是在具有控制器和 views 的 ASP.NET Core 應用程式中使用的[ Razor 視圖](xref:tutorials/first-mvc-app/adding-view)檔案。</span><span class="sxs-lookup"><span data-stu-id="90fa5-447">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="90fa5-448">讓它不同的是 `@page` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="90fa5-448">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="90fa5-449">`@page` 會將檔案轉換成 MVC 動作，這表示它會直接處理要求，不用透過控制器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-449">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="90fa5-450">`@page` 必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-450">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="90fa5-451">`@page` 會影響其他結構的行為 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-451">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="90fa5-452">使用`PageModel`類別的類似頁面，顯示於下列兩個檔案中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-452">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="90fa5-453">*Pages/Index2.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-453">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="90fa5-454">*Pages/Index2.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-454">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="90fa5-455">依照慣例，類別檔案的名稱會與 `PageModel` Razor 附加 *.cs* 的分頁檔相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-455">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="90fa5-456">例如，前一 Razor 頁是 *Pages/index2.cshtml.cs。*</span><span class="sxs-lookup"><span data-stu-id="90fa5-456">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="90fa5-457">包含 `PageModel` 類別的檔案名為 *Pages/Index2.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="90fa5-457">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="90fa5-458">頁面的 URL 路徑關聯是由頁面在檔案系統中的位置決定。</span><span class="sxs-lookup"><span data-stu-id="90fa5-458">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="90fa5-459">下表顯示 Razor 頁面路徑和相符的 URL：</span><span class="sxs-lookup"><span data-stu-id="90fa5-459">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="90fa5-460">檔案名稱和路徑</span><span class="sxs-lookup"><span data-stu-id="90fa5-460">File name and path</span></span>               | <span data-ttu-id="90fa5-461">比對 URL</span><span class="sxs-lookup"><span data-stu-id="90fa5-461">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="90fa5-462">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-462">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="90fa5-463">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="90fa5-463">`/` or `/Index`</span></span> |
| <span data-ttu-id="90fa5-464">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-464">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="90fa5-465">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-465">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="90fa5-466">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-466">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="90fa5-467">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="90fa5-467">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="90fa5-468">注意：</span><span class="sxs-lookup"><span data-stu-id="90fa5-468">Notes:</span></span>

* <span data-ttu-id="90fa5-469">執行時間預設會 Razor 在 [ *pages* ] 資料夾中尋找分頁檔。</span><span class="sxs-lookup"><span data-stu-id="90fa5-469">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="90fa5-470">`Index` 是 URL 未包含頁面時的預設頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-470">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="90fa5-471">撰寫基本表單</span><span class="sxs-lookup"><span data-stu-id="90fa5-471">Write a basic form</span></span>

<span data-ttu-id="90fa5-472">Razor 頁面的設計目的是要讓搭配網頁瀏覽器使用的常見模式在建立應用程式時很容易執行。</span><span class="sxs-lookup"><span data-stu-id="90fa5-472">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="90fa5-473">[模型](xref:mvc/models/model-binding)系結 [、卷](xref:mvc/views/tag-helpers/intro)標協助程式和 HTML 協助程式全都 *是* 使用頁面類別中定義的屬性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-473">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="90fa5-474">`Contact` 模型請考慮實作基本的「與我們連絡」格式頁面：</span><span class="sxs-lookup"><span data-stu-id="90fa5-474">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="90fa5-475">本文件中的範例，會在 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 檔案中初始化 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-475">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="90fa5-476">資料模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-476">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="90fa5-477">DB 內容：</span><span class="sxs-lookup"><span data-stu-id="90fa5-477">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="90fa5-478">*Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-478">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="90fa5-479">*Pages/Create.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-479">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="90fa5-480">依照慣例，`PageModel` 類別稱之為 `<PageName>Model`，與頁面位於相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="90fa5-480">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="90fa5-481">`PageModel` 類別可以分離頁面邏輯與頁面展示。</span><span class="sxs-lookup"><span data-stu-id="90fa5-481">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="90fa5-482">此類別會定義頁面的處理常式，以處理傳送至頁面的要求與用於轉譯頁面的資料。</span><span class="sxs-lookup"><span data-stu-id="90fa5-482">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="90fa5-483">這種分隔允許：</span><span class="sxs-lookup"><span data-stu-id="90fa5-483">This separation allows:</span></span>

* <span data-ttu-id="90fa5-484">透過相依性 [插入](xref:fundamentals/dependency-injection)來管理頁面相依性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-484">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="90fa5-485">[單元測試](xref:test/razor-pages-tests) 頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-485">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="90fa5-486">在 `POST` 要求上執行的頁面具有 「處理常式方法」`OnPostAsync`  (當使用者張貼表單時)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-486">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="90fa5-487">您可以新增任何 HTTP 指令動詞的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-487">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="90fa5-488">最常見的處理常式包括：</span><span class="sxs-lookup"><span data-stu-id="90fa5-488">The most common handlers are:</span></span>

* <span data-ttu-id="90fa5-489">`OnGet`，初始化頁所需要的狀態。</span><span class="sxs-lookup"><span data-stu-id="90fa5-489">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="90fa5-490">[OnGet](#OnGet) 範例。</span><span class="sxs-lookup"><span data-stu-id="90fa5-490">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="90fa5-491">`OnPost`，處理表單提交作業。</span><span class="sxs-lookup"><span data-stu-id="90fa5-491">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="90fa5-492">`Async` 命名尾碼是選擇性的，但依照慣例通常用於非同步函式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-492">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="90fa5-493">上述程式碼一般適用于 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-493">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="90fa5-494">如果您熟悉使用控制器和 views 的 ASP.NET apps：</span><span class="sxs-lookup"><span data-stu-id="90fa5-494">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="90fa5-495">`OnPostAsync`上述範例中的程式碼看起來類似一般的控制器程式碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-495">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="90fa5-496">大部分的 MVC 基本專案，例如 [模型](xref:mvc/models/model-binding)系結、 [驗證](xref:mvc/models/validation)、 [驗證](xref:mvc/models/validation)和動作結果都是共用的。</span><span class="sxs-lookup"><span data-stu-id="90fa5-496">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="90fa5-497">前一個 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="90fa5-497">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="90fa5-498">`OnPostAsync` 的基本流程：</span><span class="sxs-lookup"><span data-stu-id="90fa5-498">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="90fa5-499">檢查驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="90fa5-499">Check for validation errors.</span></span>

* <span data-ttu-id="90fa5-500">如果沒有任何錯誤，會儲存資料並重新導向。</span><span class="sxs-lookup"><span data-stu-id="90fa5-500">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="90fa5-501">如果有錯誤，會再次顯示有驗證訊息的頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-501">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="90fa5-502">用戶端驗證和傳統的 ASP.NET Core MVC 應用程式完全相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-502">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="90fa5-503">在許多情況下，會在用戶端上偵測到驗證錯誤，且永遠不會提交到伺服器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-503">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="90fa5-504">成功輸入資料後，`OnPostAsync` 處理常式方法會呼叫 `RedirectToPage` 協助程式方法，傳回 `RedirectToPageResult` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="90fa5-504">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="90fa5-505">`RedirectToPage` 是新的動作結果，類似於 `RedirectToAction` 或 `RedirectToRoute`，但會針對頁面自訂。</span><span class="sxs-lookup"><span data-stu-id="90fa5-505">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="90fa5-506">在上述範例中，它會重新導向至根索引頁面 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-506">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="90fa5-507">[產生頁面 URL](#url_gen)一節會詳細說明 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-507">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="90fa5-508">當提交的表單有驗證錯誤時 (傳遞至伺服器)，`OnPostAsync` 處理常式方法會呼叫 `Page` 協助程式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-508">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="90fa5-509">`Page` 會傳回 `PageResult` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="90fa5-509">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="90fa5-510">傳回 `Page` 類似於控制站中的動作傳回 `View`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-510">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="90fa5-511">`PageResult` 這是處理常式方法的預設傳回型別。</span><span class="sxs-lookup"><span data-stu-id="90fa5-511">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="90fa5-512">傳回 `void` 的處理常式方法會呈現頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-512">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="90fa5-513">`Customer` 屬性 (property) 使用 `[BindProperty]` 屬性 (attribute) 加入模型繫結。</span><span class="sxs-lookup"><span data-stu-id="90fa5-513">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="90fa5-514">Razor 依預設，頁面只會系結具有非動詞命令的屬性 `GET` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-514">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="90fa5-515">繫結至屬性可以減少您必須撰寫的程式碼數量。</span><span class="sxs-lookup"><span data-stu-id="90fa5-515">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="90fa5-516">透過使用相同的屬性呈現表單欄位 (`<input asp-for="Customer.Name">`) 並接受輸入，繫結可以減少程式碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-516">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="90fa5-517">首頁 (*Index.cshtml*)：</span><span class="sxs-lookup"><span data-stu-id="90fa5-517">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="90fa5-518">已建立關聯的 `PageModel` 類別 (*Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="90fa5-518">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="90fa5-519">*Index.cshtml* 檔案包含下列標記可為每個連絡人建立編輯連結：</span><span class="sxs-lookup"><span data-stu-id="90fa5-519">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="90fa5-520">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>`[錨點](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)標籤協助程式使用 `asp-route-{value}` 屬性來產生編輯頁面的連結。</span><span class="sxs-lookup"><span data-stu-id="90fa5-520">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="90fa5-521">該連結包含路由資料和連絡人識別碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-521">The link contains route data with the contact ID.</span></span> <span data-ttu-id="90fa5-522">例如： `https://localhost:5001/Edit/1` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-522">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="90fa5-523">[標記](xref:mvc/views/tag-helpers/intro) 協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-523">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="90fa5-524">標記協助程式由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 啟用</span><span class="sxs-lookup"><span data-stu-id="90fa5-524">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="90fa5-525">*Pages/Edit.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-525">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="90fa5-526">第一行包含 `@page "{id:int}"` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="90fa5-526">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="90fa5-527">路由條件約束 `"{id:int}"` 通知頁面接受包含 `int` 路由資料的頁面要求。</span><span class="sxs-lookup"><span data-stu-id="90fa5-527">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="90fa5-528">如果頁面要求不包含可以轉換成 `int` 的路由資料，執行階段會傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="90fa5-528">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="90fa5-529">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="90fa5-529">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="90fa5-530">*Pages/Edit.cshtml.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-530">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="90fa5-531">*Index.cshtml* 檔案也包含能夠為每個客戶連絡人建立刪除按鈕的標記：</span><span class="sxs-lookup"><span data-stu-id="90fa5-531">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="90fa5-532">使用 HTML 轉譯刪除按鈕時，其 `formaction` 會包含下列項目的參數：</span><span class="sxs-lookup"><span data-stu-id="90fa5-532">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="90fa5-533">`asp-route-id` 屬性指定的客戶連絡人識別碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-533">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="90fa5-534">`asp-page-handler` 屬性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-534">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="90fa5-535">以下是轉譯的刪除按鈕範例，內含客戶連絡人識別碼 `1`：</span><span class="sxs-lookup"><span data-stu-id="90fa5-535">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="90fa5-536">選取按鈕時，表單 `POST` 要求會傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="90fa5-536">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="90fa5-537">依照慣例，會依據配置 `OnPost[handler]Async`，按 `handler` 參數的值來選取處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-537">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="90fa5-538">在此範例中，因為 `handler` 為 `delete`，所以會使用 `OnPostDeleteAsync` 處理常式方法來處理 `POST` 要求。</span><span class="sxs-lookup"><span data-stu-id="90fa5-538">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="90fa5-539">若 `asp-page-handler` 設為其他值 (例如 `remove`)，則會選取名為 `OnPostRemoveAsync` 的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-539">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="90fa5-540">下列程式碼顯示 `OnPostDeleteAsync` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="90fa5-540">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="90fa5-541">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="90fa5-541">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="90fa5-542">接受查詢字串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-542">Accepts the `id` from the query string.</span></span> <span data-ttu-id="90fa5-543">如果 *Index. cshtml* 頁面指示詞包含路由條件約束 `"{id:int?}"` ，則 `id` 會來自路由資料。</span><span class="sxs-lookup"><span data-stu-id="90fa5-543">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="90fa5-544">的路由資料 `id` 是在 URI 中指定，例如 `https://localhost:5001/Customers/2` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-544">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="90fa5-545">使用 `FindAsync` 在資料庫中查詢客戶連絡人。</span><span class="sxs-lookup"><span data-stu-id="90fa5-545">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="90fa5-546">若找到客戶連絡人，會從客戶連絡人清單中予以移除。</span><span class="sxs-lookup"><span data-stu-id="90fa5-546">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="90fa5-547">資料庫隨即更新。</span><span class="sxs-lookup"><span data-stu-id="90fa5-547">The database is updated.</span></span>
* <span data-ttu-id="90fa5-548">呼叫 `RedirectToPage` 以重新導向至根索引頁 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-548">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="90fa5-549">將頁面屬性標示為必要</span><span class="sxs-lookup"><span data-stu-id="90fa5-549">Mark page properties as required</span></span>

<span data-ttu-id="90fa5-550">上的屬性 `PageModel` 可以使用 [必要](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) 的屬性標記：</span><span class="sxs-lookup"><span data-stu-id="90fa5-550">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="90fa5-551">如需詳細資訊，請參閱[模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-551">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="90fa5-552">使用 OnGet 處理常式後援來處理 HEAD 要求</span><span class="sxs-lookup"><span data-stu-id="90fa5-552">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="90fa5-553">`HEAD` 要求可讓您擷取特定資源的標頭。</span><span class="sxs-lookup"><span data-stu-id="90fa5-553">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="90fa5-554">不同於 `GET` 要求，`HEAD` 要求不會傳回回應主體。</span><span class="sxs-lookup"><span data-stu-id="90fa5-554">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="90fa5-555">一般來說，會為 `HEAD` 要求建立及呼叫 `OnHead` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="90fa5-555">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="90fa5-556">在 ASP.NET Core 2.1 或更新版本中， Razor `OnGet` 如果未定義任何處理程式，頁面就會切換回呼叫處理常式 `OnHead` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-556">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="90fa5-557">這個行為藉由在 `Startup.ConfigureServices` 中呼叫 [SetCompatibilityVersion](xref:mvc/compatibility-version) 來啟用：</span><span class="sxs-lookup"><span data-stu-id="90fa5-557">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="90fa5-558">預設範本會在 ASP.NET Core 2.1 和 2.2 中產生 `SetCompatibilityVersion` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="90fa5-558">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="90fa5-559">`SetCompatibilityVersion` 有效地將 Razor Pages 選項設定 `AllowMappingHeadRequestsToGetHandler` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-559">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="90fa5-560">您可以明確選擇「特定」行為，而不必透過 `SetCompatibilityVersion` 選擇所有行為。</span><span class="sxs-lookup"><span data-stu-id="90fa5-560">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="90fa5-561">下列程式碼會選擇讓 `HEAD` 要求對應到 `OnGet` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="90fa5-561">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="90fa5-562">XSRF/CSRF 和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="90fa5-562">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="90fa5-563">您不必撰寫任何[防偽驗證](xref:security/anti-request-forgery)程式碼。</span><span class="sxs-lookup"><span data-stu-id="90fa5-563">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="90fa5-564">Antiforgery 權杖的產生和驗證會自動包含在 Razor 頁面中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-564">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="90fa5-565">使用頁面的版面配置、部分、範本和標記協助程式 Razor</span><span class="sxs-lookup"><span data-stu-id="90fa5-565">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="90fa5-566">頁面會使用 view engine 的所有功能 Razor 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-566">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="90fa5-567">配置、部分、範本、標籤協助程式、 *_ViewStart cshtml*、 *_ViewImports. cshtml* 的運作方式與傳統視圖的運作方式相同。 Razor</span><span class="sxs-lookup"><span data-stu-id="90fa5-567">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="90fa5-568">可利用這些功能的一部分來整理這個頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-568">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="90fa5-569">將 [版面配置頁面](xref:mvc/views/layout)新增至 *Pages/Shared/_Layout.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="90fa5-569">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="90fa5-570">[版面](xref:mvc/views/layout)配置：</span><span class="sxs-lookup"><span data-stu-id="90fa5-570">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="90fa5-571">控制每個頁面的版面配置 (除非頁面退出版面配置)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-571">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="90fa5-572">匯入 HTML 結構，例如 JavaScript 和樣式表。</span><span class="sxs-lookup"><span data-stu-id="90fa5-572">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="90fa5-573">如需詳細資訊，請參閱[版面配置頁面](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-573">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="90fa5-574">[版面配置](xref:mvc/views/layout#specifying-a-layout)屬性是在 *Pages/_ViewStart.cshtml* 中設定：</span><span class="sxs-lookup"><span data-stu-id="90fa5-574">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="90fa5-575">版面配置位於 *Pages/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-575">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="90fa5-576">頁面會以階層方式尋找其他檢視 (版面配置、範本、部分)，從目前頁面的相同資料夾開始。</span><span class="sxs-lookup"><span data-stu-id="90fa5-576">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="90fa5-577">*頁面/共用* 資料夾中的版面配置可以從 Razor [ *pages* ] 資料夾下的任何頁面使用。</span><span class="sxs-lookup"><span data-stu-id="90fa5-577">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="90fa5-578">版面配置頁面應位於 *Pages/Shared* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-578">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="90fa5-579">我們 **不** 建議您將配置檔案放入 *Views/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-579">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="90fa5-580">*Views/Shared* 是 MVC 檢視模式。</span><span class="sxs-lookup"><span data-stu-id="90fa5-580">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="90fa5-581">Razor 頁面的目的是要依賴資料夾階層，不是路徑慣例。</span><span class="sxs-lookup"><span data-stu-id="90fa5-581">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="90fa5-582">頁面上的視圖搜尋 Razor 包含 *Pages* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-582">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="90fa5-583">您使用 MVC 控制器和傳統視圖的版面配置、範本和部分， Razor *只是工作*。</span><span class="sxs-lookup"><span data-stu-id="90fa5-583">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="90fa5-584">新增 *Pages/_ViewImports.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-584">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="90fa5-585">本教學課程稍後會說明 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-585">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="90fa5-586">`@addTagHelper` 指示詞會將 [內建標記協助程式](xref:mvc/views/tag-helpers/builtin-th/Index)帶入 *Pages* 資料夾中的所有頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-586">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="90fa5-587">在頁面上明確使用 `@namespace` 指示詞時：</span><span class="sxs-lookup"><span data-stu-id="90fa5-587">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="90fa5-588">指示詞會設定頁面的命名空間。</span><span class="sxs-lookup"><span data-stu-id="90fa5-588">The directive sets the namespace for the page.</span></span> <span data-ttu-id="90fa5-589">`@model` 指示詞不需要包含命名空間。</span><span class="sxs-lookup"><span data-stu-id="90fa5-589">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="90fa5-590">當 `@namespace` 指示詞包含在 *_ViewImports.cshtml* 中時，指定的命名空間會在匯入 `@namespace` 指示詞的頁面中提供所產生之命名空間的前置詞。</span><span class="sxs-lookup"><span data-stu-id="90fa5-590">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="90fa5-591">所產生命名空間的其餘部分 (後置字元部分) 是包含 *_ViewImports.cshtml* 的資料夾和包含頁面的資料夾之間，以句點分隔的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="90fa5-591">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="90fa5-592">例如，`PageModel` 類別 *Pages/Customers/Edit.cshtml.cs* 會明確地設定命名空間：</span><span class="sxs-lookup"><span data-stu-id="90fa5-592">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="90fa5-593">*Pages/_ViewImports.cshtml* 檔案會設定下列命名空間：</span><span class="sxs-lookup"><span data-stu-id="90fa5-593">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="90fa5-594">針對 *Pages/Customers/Edit. cshtml* 頁面產生的命名空間與 Razor `PageModel` 類別相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-594">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="90fa5-595">`@namespace`*也可搭配傳統 Razor 視圖使用。*</span><span class="sxs-lookup"><span data-stu-id="90fa5-595">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="90fa5-596">原始的 *Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-596">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="90fa5-597">更新的 *Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="90fa5-597">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="90fa5-598">[ Razor Pages 入門專案](#rpvs17)包含 *pages/_ValidationScriptsPartial. cshtml*，可連結用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="90fa5-598">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="90fa5-599">如需部分檢視的詳細資訊，請參閱 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="90fa5-599">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="90fa5-600">產生頁面 URL</span><span class="sxs-lookup"><span data-stu-id="90fa5-600">URL generation for Pages</span></span>

<span data-ttu-id="90fa5-601">前面出現過的 `Create` 頁面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="90fa5-601">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="90fa5-602">應用程式有下列檔案/資料夾結構：</span><span class="sxs-lookup"><span data-stu-id="90fa5-602">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="90fa5-603">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="90fa5-603">*/Pages*</span></span>

  * <span data-ttu-id="90fa5-604">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-604">*Index.cshtml*</span></span>
  * <span data-ttu-id="90fa5-605">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="90fa5-605">*/Customers*</span></span>

    * <span data-ttu-id="90fa5-606">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-606">*Create.cshtml*</span></span>
    * <span data-ttu-id="90fa5-607">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-607">*Edit.cshtml*</span></span>
    * <span data-ttu-id="90fa5-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="90fa5-608">*Index.cshtml*</span></span>

<span data-ttu-id="90fa5-609">*Pages/Customers/Create.cshtml* 和 *Pages/Customers/Edit.cshtml* 頁面在成功後會重新導向至 *Pages/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="90fa5-609">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="90fa5-610">字串 `/Index` 為 URI 的一部分，可存取前一個頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-610">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="90fa5-611">字串 `/Index` 可以用來產生 *Pages/Index.cshtml* 頁面的 URI。</span><span class="sxs-lookup"><span data-stu-id="90fa5-611">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="90fa5-612">例如：</span><span class="sxs-lookup"><span data-stu-id="90fa5-612">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="90fa5-613">頁面名稱是從根 */Pages* 資料夾到該頁面的路徑 (包括前置的 `/`，例如 `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-613">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="90fa5-614">上述 URL 產生範例，透過硬式編碼的 URL 提供更加優異的選項與功能。</span><span class="sxs-lookup"><span data-stu-id="90fa5-614">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="90fa5-615">URL 產生使用[路由](xref:mvc/controllers/routing)，可以根據路由在目的地路徑中定義的方式，產生並且編碼參數。</span><span class="sxs-lookup"><span data-stu-id="90fa5-615">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="90fa5-616">產生頁面 URL 支援相關的名稱。</span><span class="sxs-lookup"><span data-stu-id="90fa5-616">URL generation for pages supports relative names.</span></span> <span data-ttu-id="90fa5-617">下表顯示從 *Pages/Customers/Create.cshtml* 以不同的 `RedirectToPage` 參數選取的索引頁：</span><span class="sxs-lookup"><span data-stu-id="90fa5-617">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="90fa5-618">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="90fa5-618">RedirectToPage(x)</span></span>| <span data-ttu-id="90fa5-619">頁面</span><span class="sxs-lookup"><span data-stu-id="90fa5-619">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="90fa5-620">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="90fa5-620">RedirectToPage("/Index")</span></span> | <span data-ttu-id="90fa5-621">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-621">*Pages/Index*</span></span> |
| <span data-ttu-id="90fa5-622">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="90fa5-622">RedirectToPage("./Index");</span></span> | <span data-ttu-id="90fa5-623">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-623">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="90fa5-624">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="90fa5-624">RedirectToPage("../Index")</span></span> | <span data-ttu-id="90fa5-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-625">*Pages/Index*</span></span> |
| <span data-ttu-id="90fa5-626">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="90fa5-626">RedirectToPage("Index")</span></span>  | <span data-ttu-id="90fa5-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="90fa5-627">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="90fa5-628">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是「相對名稱」。</span><span class="sxs-lookup"><span data-stu-id="90fa5-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="90fa5-629">`RedirectToPage` 參數「結合」了目前頁面的路徑，以計算目的地頁面的名稱。</span><span class="sxs-lookup"><span data-stu-id="90fa5-629">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="90fa5-630">相對名稱連結在以複雜結構建置網站時很有用。</span><span class="sxs-lookup"><span data-stu-id="90fa5-630">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="90fa5-631">如果您使用相對名稱連結資料夾中的頁面，您可以重新命名該資料夾。</span><span class="sxs-lookup"><span data-stu-id="90fa5-631">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="90fa5-632">所有連結仍可運作 (因為它們不包含資料夾名稱)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-632">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="90fa5-633">若要重新導向到不同[區域](xref:mvc/controllers/areas)中的頁面，請指定區域：</span><span class="sxs-lookup"><span data-stu-id="90fa5-633">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="90fa5-634">如需詳細資訊，請參閱<xref:mvc/controllers/areas>。</span><span class="sxs-lookup"><span data-stu-id="90fa5-634">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="90fa5-635">ViewData 屬性</span><span class="sxs-lookup"><span data-stu-id="90fa5-635">ViewData attribute</span></span>

<span data-ttu-id="90fa5-636">資料可以傳遞至具有 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) 的頁面。</span><span class="sxs-lookup"><span data-stu-id="90fa5-636">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="90fa5-637">Razor具有屬性之控制器或頁面模型上的屬性 `[ViewData]` 會從[>viewdatadictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)儲存和載入其值。</span><span class="sxs-lookup"><span data-stu-id="90fa5-637">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="90fa5-638">在下列範例中， `AboutModel` 包含標示為的 `Title` 屬性 `[ViewData]` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-638">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="90fa5-639">`Title` 屬性會設定為 [關於] 頁面的標題：</span><span class="sxs-lookup"><span data-stu-id="90fa5-639">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="90fa5-640">在 [關於] 頁面上，存取 `Title` 屬性作為模型屬性：</span><span class="sxs-lookup"><span data-stu-id="90fa5-640">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="90fa5-641">在此配置中，標題會從 ViewData 字典中讀取：</span><span class="sxs-lookup"><span data-stu-id="90fa5-641">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="90fa5-642">TempData</span><span class="sxs-lookup"><span data-stu-id="90fa5-642">TempData</span></span>

<span data-ttu-id="90fa5-643">ASP.NET Core 公開[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)上的 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) 屬性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-643">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="90fa5-644">這個屬性會儲存資料，直到讀取為止。</span><span class="sxs-lookup"><span data-stu-id="90fa5-644">This property stores data until it's read.</span></span> <span data-ttu-id="90fa5-645">`Keep` 和 `Peek` 方法可以用來檢查資料，不用刪除。</span><span class="sxs-lookup"><span data-stu-id="90fa5-645">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="90fa5-646">當有多個要求需要資料時，`TempData` 對重新導向很有幫助。</span><span class="sxs-lookup"><span data-stu-id="90fa5-646">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="90fa5-647">下列程式碼會設定使用 `TempData` 的 `Message` 值：</span><span class="sxs-lookup"><span data-stu-id="90fa5-647">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="90fa5-648">*Pages/Customers/Index.cshtml* 檔案中的下列標記會顯示使用 `TempData` 的 `Message` 值。</span><span class="sxs-lookup"><span data-stu-id="90fa5-648">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="90fa5-649">*Pages/Customers/Index.cshtml.cs* 頁面模型會將 `[TempData]` 屬性 (attribute) 套用到 `Message` 屬性 (property)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-649">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="90fa5-650">如需詳細資訊，請參閱 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-650">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="90fa5-651">每頁面有多個處理常式</span><span class="sxs-lookup"><span data-stu-id="90fa5-651">Multiple handlers per page</span></span>

<span data-ttu-id="90fa5-652">下列頁面會使用 `asp-page-handler` 標記協助程式為兩個處理常式產生標記：</span><span class="sxs-lookup"><span data-stu-id="90fa5-652">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="90fa5-653">上例中的表單有兩個提交按鈕，每一個都使用 `FormActionTagHelper` 提交至不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="90fa5-653">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="90fa5-654">`asp-page-handler` 屬性附隨於 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-654">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="90fa5-655">`asp-page-handler` 產生的 URL 會提交至頁面所定義的每一個處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="90fa5-655">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="90fa5-656">因為範例連結至目前的頁面，所以未指定 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-656">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="90fa5-657">頁面模型：</span><span class="sxs-lookup"><span data-stu-id="90fa5-657">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="90fa5-658">上述程式碼使用「具名的處理常式方法」。</span><span class="sxs-lookup"><span data-stu-id="90fa5-658">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="90fa5-659">具名的處理常式方法的建立方式是採用名稱中在 `On<HTTP Verb>` 後面、`Async` 之前 (如有) 的文字。</span><span class="sxs-lookup"><span data-stu-id="90fa5-659">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="90fa5-660">在上例中，頁面方法是 OnPost **JoinList** Async 和 OnPost **JoinListUC** Async。</span><span class="sxs-lookup"><span data-stu-id="90fa5-660">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="90fa5-661">移除 *OnPost* 和 *Async*，處理常式名稱就是 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-661">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="90fa5-662">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-662">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="90fa5-663">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-663">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="90fa5-664">自訂路由</span><span class="sxs-lookup"><span data-stu-id="90fa5-664">Custom routes</span></span>

<span data-ttu-id="90fa5-665">使用 `@page` 指示詞，可以：</span><span class="sxs-lookup"><span data-stu-id="90fa5-665">Use the `@page` directive to:</span></span>

* <span data-ttu-id="90fa5-666">指定頁面的自訂路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-666">Specify a custom route to a page.</span></span> <span data-ttu-id="90fa5-667">例如，[關於] 頁面的路由可使用 `@page "/Some/Other/Path"` 設為 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-667">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="90fa5-668">將區段附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-668">Append segments to a page's default route.</span></span> <span data-ttu-id="90fa5-669">例如，使用 `@page "item"` 可將 "item" 區段新增到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-669">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="90fa5-670">將參數附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="90fa5-670">Append parameters to a page's default route.</span></span> <span data-ttu-id="90fa5-671">例如，具有 `@page "{id}"` 的頁面可要求識別碼參數 `id`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-671">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="90fa5-672">支援在路徑開頭以波狀符號 (`~`) 指定根相對路徑。</span><span class="sxs-lookup"><span data-stu-id="90fa5-672">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="90fa5-673">例如，`@page "~/Some/Other/Path"` 與 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="90fa5-673">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="90fa5-674">如果您不喜歡 URL 中的查詢字串 `?handler=JoinList` ，請變更路由，將處理常式名稱放在 url 的路徑部分。</span><span class="sxs-lookup"><span data-stu-id="90fa5-674">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="90fa5-675">您可以藉由新增以雙引號括住的路由範本來自訂路由 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="90fa5-675">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="90fa5-676">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-676">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="90fa5-677">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="90fa5-677">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="90fa5-678">跟在 `handler` 後面的 `?` 表示路由參數為選擇性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-678">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="90fa5-679">組態與設定</span><span class="sxs-lookup"><span data-stu-id="90fa5-679">Configuration and settings</span></span>

<span data-ttu-id="90fa5-680">若要設定進階選項，請在 MVC 產生器上使用擴充方法 `AddRazorPagesOptions`：</span><span class="sxs-lookup"><span data-stu-id="90fa5-680">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="90fa5-681">目前可以使用 `RazorPagesOptions` 設定頁面的根目錄，或新增頁面的應用程式模型慣例。</span><span class="sxs-lookup"><span data-stu-id="90fa5-681">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="90fa5-682">我們將在未來以這種方式獲得更多的擴充性。</span><span class="sxs-lookup"><span data-stu-id="90fa5-682">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="90fa5-683">若要先行編譯視圖，請參閱[ Razor view 編譯](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-683">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="90fa5-684">[下載或檢視範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-684">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="90fa5-685">請參閱這篇簡介中的 [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="90fa5-685">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="90fa5-686">指定 Razor 頁面位於內容根目錄</span><span class="sxs-lookup"><span data-stu-id="90fa5-686">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="90fa5-687">根據預設， Razor 頁面會根目錄在 */Pages* 目錄中。</span><span class="sxs-lookup"><span data-stu-id="90fa5-687">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="90fa5-688">將 [ Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) 新增至 [>addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ，以指定您的 Razor 頁面位於應用程式的 [內容根目錄](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) ：</span><span class="sxs-lookup"><span data-stu-id="90fa5-688">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="90fa5-689">指定 Razor 頁面位於自訂根目錄</span><span class="sxs-lookup"><span data-stu-id="90fa5-689">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="90fa5-690">將 [ Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) 新增至 [>addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ，以指定您 Razor 的頁面位於應用程式中的自訂根目錄， (提供相對路徑) ：</span><span class="sxs-lookup"><span data-stu-id="90fa5-690">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="90fa5-691">其他資源</span><span class="sxs-lookup"><span data-stu-id="90fa5-691">Additional resources</span></span>

* [<span data-ttu-id="90fa5-692">授權屬性和 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="90fa5-692">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
