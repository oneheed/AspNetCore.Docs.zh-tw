---
title: RazorASP.NET Core 中的頁面簡介
author: Rick-Anderson
description: 瞭解 Razor ASP.NET Core 中的頁面如何讓撰寫以頁面為焦點的案例更輕鬆且更具生產力，而不是使用 MVC。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
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
ms.openlocfilehash: 203d77a2caaba9156419a04754fe162dd57052c1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012705"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="1d0f6-103">RazorASP.NET Core 中的頁面簡介</span><span class="sxs-lookup"><span data-stu-id="1d0f6-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d0f6-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="1d0f6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="1d0f6-105">Razor頁面可讓撰寫以頁面為焦點的案例更輕鬆且更具生產力，而不是使用控制器和視圖。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="1d0f6-106">如果您在尋找使用模型檢視控制器方法的教學課程，請參閱[開始使用 ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="1d0f6-107">本檔提供 Razor 頁面簡介。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="1d0f6-108">它不是逐步教學課程。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="1d0f6-109">如果您發現某些區段太先進，請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="1d0f6-110">如需 ASP.NET Core 的概觀，請參閱[ASP.NET Core 簡介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1d0f6-111">必要條件</span><span class="sxs-lookup"><span data-stu-id="1d0f6-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d0f6-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d0f6-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d0f6-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d0f6-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d0f6-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1d0f6-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="1d0f6-115">建立 Razor 頁面專案</span><span class="sxs-lookup"><span data-stu-id="1d0f6-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d0f6-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d0f6-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1d0f6-117">如需如何建立頁面專案的詳細指示，請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start) Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d0f6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d0f6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1d0f6-119">從命令列執行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d0f6-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1d0f6-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1d0f6-121">如需如何建立頁面專案的詳細指示，請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start) Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="1d0f6-122">Razor頁面</span><span class="sxs-lookup"><span data-stu-id="1d0f6-122">Razor Pages</span></span>

<span data-ttu-id="1d0f6-123">Razor頁面已在*Startup.cs*中啟用：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="1d0f6-124">請考慮使用基本頁面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="1d0f6-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-125">上述程式碼看起來很像是在具有控制器和 views 的 ASP.NET Core 應用程式中使用的[ Razor 視圖](xref:tutorials/first-mvc-app/adding-view)檔案。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="1d0f6-126">這會讓指示詞不同 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="1d0f6-127">`@page` 會將檔案轉換成 MVC 動作，這表示它會直接處理要求，不用透過控制器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="1d0f6-128">`@page`必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1d0f6-129">`@page`會影響其他結構的行為 [Razor](xref:mvc/views/razor) 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="1d0f6-130">Razor分頁檔名的開頭為 *..* \* 尾碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="1d0f6-131">使用`PageModel`類別的類似頁面，顯示於下列兩個檔案中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="1d0f6-132">*Pages/Index2.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="1d0f6-133">*Pages/Index2.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="1d0f6-134">依照慣例， `PageModel` 類別檔案的名稱與分頁檔相同， Razor 附加 *.cs* 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="1d0f6-135">例如，前一 Razor 頁是*Pages/Index2*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="1d0f6-136">包含 `PageModel` 類別的檔案名為 *Pages/Index2.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="1d0f6-137">頁面的 URL 路徑關聯是由頁面在檔案系統中的位置決定。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="1d0f6-138">下表顯示 Razor 頁面路徑和相符的 URL：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="1d0f6-139">檔案名稱和路徑</span><span class="sxs-lookup"><span data-stu-id="1d0f6-139">File name and path</span></span>               | <span data-ttu-id="1d0f6-140">比對 URL</span><span class="sxs-lookup"><span data-stu-id="1d0f6-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1d0f6-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="1d0f6-142">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="1d0f6-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="1d0f6-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="1d0f6-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="1d0f6-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="1d0f6-146">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="1d0f6-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="1d0f6-147">注意：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-147">Notes:</span></span>

* <span data-ttu-id="1d0f6-148">執行時間預設會 Razor 在*pages*資料夾中尋找分頁檔案。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="1d0f6-149">`Index` 是 URL 未包含頁面時的預設頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="1d0f6-150">撰寫基本表單</span><span class="sxs-lookup"><span data-stu-id="1d0f6-150">Write a basic form</span></span>

<span data-ttu-id="1d0f6-151">Razor頁面的設計目的是要在建立應用程式時，讓與網頁瀏覽器搭配使用的常見模式變得容易執行。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="1d0f6-152">[模型](xref:mvc/models/model-binding)系結、[標記](xref:mvc/views/tag-helpers/intro)協助程式和 HTML 協助程式都*只*會使用頁面類別中定義的屬性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="1d0f6-153">`Contact` 模型請考慮實作基本的「與我們連絡」格式頁面：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="1d0f6-154">本文件中的範例，會在 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) 檔案中初始化 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="1d0f6-155">記憶體中的資料庫需要 `Microsoft.EntityFrameworkCore.InMemory` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="1d0f6-156">資料模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="1d0f6-157">DB 內容：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="1d0f6-158">*Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="1d0f6-159">*Pages/Create.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="1d0f6-160">依照慣例，`PageModel` 類別稱之為 `<PageName>Model`，與頁面位於相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="1d0f6-161">`PageModel` 類別可以分離頁面邏輯與頁面展示。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="1d0f6-162">此類別會定義頁面的處理常式，以處理傳送至頁面的要求與用於轉譯頁面的資料。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="1d0f6-163">這種分隔允許：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-163">This separation allows:</span></span>

* <span data-ttu-id="1d0f6-164">透過相依性[插入](xref:fundamentals/dependency-injection)來管理頁面相依性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="1d0f6-165">單元測試</span><span class="sxs-lookup"><span data-stu-id="1d0f6-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="1d0f6-166">在 `POST` 要求上執行的頁面具有 「處理常式方法」`OnPostAsync` \*\* (當使用者張貼表單時)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="1d0f6-167">可以新增任何 HTTP 指令動詞的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="1d0f6-168">最常見的處理常式包括：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-168">The most common handlers are:</span></span>

* <span data-ttu-id="1d0f6-169">`OnGet`，初始化頁所需要的狀態。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="1d0f6-170">在上述程式碼中， `OnGet` 方法會顯示*CreateModel* Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="1d0f6-171">`OnPost`，處理表單提交作業。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="1d0f6-172">`Async` 命名尾碼是選擇性的，但依照慣例通常用於非同步函式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="1d0f6-173">上述程式碼通常用於 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="1d0f6-174">如果您熟悉如何使用控制器和 views 來 ASP.NET 應用程式：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="1d0f6-175">`OnPostAsync`上述範例中的程式碼看起來類似一般的控制器程式碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="1d0f6-176">大部分的 MVC 基本類型（例如[模型](xref:mvc/models/model-binding)系結、[驗證](xref:mvc/models/validation)和動作結果）在控制器和頁面上的運作方式都相同 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="1d0f6-177">前一個 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="1d0f6-178">`OnPostAsync` 的基本流程：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="1d0f6-179">檢查驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-179">Check for validation errors.</span></span>

* <span data-ttu-id="1d0f6-180">如果沒有任何錯誤，會儲存資料並重新導向。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="1d0f6-181">如果有錯誤，會再次顯示有驗證訊息的頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="1d0f6-182">在許多情況下，會在用戶端上偵測到驗證錯誤，且永遠不會提交到伺服器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="1d0f6-183">*Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="1d0f6-184">從*Pages/Create. cshtml*轉譯的 HTML：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="1d0f6-185">在先前的程式碼中，張貼表單：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="1d0f6-186">具有有效的資料：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-186">With valid data:</span></span>

  * <span data-ttu-id="1d0f6-187">`OnPostAsync`處理常式方法會呼叫 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper 方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="1d0f6-188">`RedirectToPage` 會傳回 <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="1d0f6-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="1d0f6-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="1d0f6-190">是動作結果。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-190">Is an action result.</span></span>
    * <span data-ttu-id="1d0f6-191">類似于 `RedirectToAction` 或 `RedirectToRoute` (用於控制器和 views) 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="1d0f6-192">已針對頁面自訂。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-192">Is customized for pages.</span></span> <span data-ttu-id="1d0f6-193">在上述範例中，它會重新導向至根索引頁面 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="1d0f6-194">[產生頁面 URL](#url_gen)一節會詳細說明 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="1d0f6-195">傳遞至伺服器的驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="1d0f6-196">`OnPostAsync`處理常式方法會呼叫 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper 方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="1d0f6-197">`Page` 會傳回 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="1d0f6-198">傳回 `Page` 類似於控制站中的動作傳回 `View`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="1d0f6-199">`PageResult`這是處理常式方法的預設傳回型別。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="1d0f6-200">傳回 `void` 的處理常式方法會呈現頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="1d0f6-201">在上述範例中，張貼不含值的表單會導致[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid)傳回 false。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="1d0f6-202">在此範例中，用戶端上不會顯示任何驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="1d0f6-203">本檔稍後會涵蓋驗證錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="1d0f6-204">用戶端驗證偵測到驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="1d0f6-205">資料**未**張貼至伺服器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="1d0f6-206">本檔稍後會說明用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="1d0f6-207">`Customer`屬性會使用 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 屬性來加入宣告模型系結：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="1d0f6-208">`[BindProperty]`**不**應該用於包含不應由用戶端變更之屬性的模型。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="1d0f6-209">如需詳細資訊，請參閱[防止大量指派](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="1d0f6-210">Razor根據預設，頁面只會系結屬性與非 `GET` 動詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="1d0f6-211">系結至屬性不需要撰寫程式碼來將 HTTP 資料轉換成模型型別。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="1d0f6-212">透過使用相同的屬性呈現表單欄位 (`<input asp-for="Customer.Name">`) 並接受輸入，繫結可以減少程式碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="1d0f6-213">查看*Pages/Create. cshtml* view file：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="1d0f6-214">在上述程式碼中，[輸入標記](xref:mvc/views/working-with-forms#the-input-tag-helper)協助程式會將 HTML 專案系結 `<input asp-for="Customer.Name" />` `<input>` 至 `Customer.Name` 模型運算式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="1d0f6-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)讓標籤協助程式可供使用。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="1d0f6-216">首頁</span><span class="sxs-lookup"><span data-stu-id="1d0f6-216">The home page</span></span>

<span data-ttu-id="1d0f6-217">*Index. cshtml*是首頁：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="1d0f6-218">已建立關聯的 `PageModel` 類別 (*Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1d0f6-219">*Index. cshtml*檔案包含下列標記：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="1d0f6-220">`<a /a>`[錨點](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)標籤協助程式使用 `asp-route-{value}` 屬性來產生 [編輯] 頁面的連結。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="1d0f6-221">該連結包含路由資料和連絡人識別碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="1d0f6-222">例如，`https://localhost:5001/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="1d0f6-223">[標記](xref:mvc/views/tag-helpers/intro)協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="1d0f6-224">*Index. cshtml*檔案包含標記，以建立每個客戶連絡人的刪除按鈕：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="1d0f6-225">呈現的 HTML：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="1d0f6-226">當 [刪除] 按鈕以 HTML 轉譯時，其[formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)會包含的參數：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="1d0f6-227">由屬性指定的客戶連絡人識別碼 `asp-route-id` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="1d0f6-228">`handler`屬性所指定的 `asp-page-handler` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="1d0f6-229">選取按鈕時，表單 `POST` 要求會傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="1d0f6-230">依照慣例，會依據配置 `OnPost[handler]Async`，按 `handler` 參數的值來選取處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="1d0f6-231">在此範例中，因為 `handler` 為 `delete`，所以會使用 `OnPostDeleteAsync` 處理常式方法來處理 `POST` 要求。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="1d0f6-232">若 `asp-page-handler` 設為其他值 (例如 `remove`)，則會選取名為 `OnPostRemoveAsync` 的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1d0f6-233">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="1d0f6-234">`id`從查詢字串取得。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="1d0f6-235">使用 `FindAsync` 在資料庫中查詢客戶連絡人。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="1d0f6-236">如果找到客戶連絡人，就會將其移除並更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="1d0f6-237">呼叫 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> 以重新導向至根索引頁 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="1d0f6-238">編輯 cshtml 檔案</span><span class="sxs-lookup"><span data-stu-id="1d0f6-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-239">第一行包含 `@page "{id:int}"` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="1d0f6-240">路由條件約束 `"{id:int}"` 通知頁面接受包含 `int` 路由資料的頁面要求。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="1d0f6-241">如果頁面要求不包含可以轉換成 `int` 的路由資料，執行階段會傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1d0f6-242">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1d0f6-243">*Edit.cshtml.cs*檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="1d0f6-244">驗證</span><span class="sxs-lookup"><span data-stu-id="1d0f6-244">Validation</span></span>

<span data-ttu-id="1d0f6-245">驗證規則：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-245">Validation rules:</span></span>

* <span data-ttu-id="1d0f6-246">在模型類別中是以宣告方式指定。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="1d0f6-247">會在應用程式的任何位置強制執行。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="1d0f6-248"><xref:System.ComponentModel.DataAnnotations>命名空間提供一組內建的驗證屬性，會以宣告方式套用至類別或屬性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="1d0f6-249">DataAnnotations 也包含格式化屬性，像 [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 是格式化的說明，而且不提供任何驗證。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="1d0f6-250">請考慮 `Customer` 下列模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="1d0f6-251">使用下列*建立. cshtml*視圖檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="1d0f6-252">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-252">The preceding code:</span></span>

* <span data-ttu-id="1d0f6-253">包含 jQuery 和 jQuery 驗證腳本。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="1d0f6-254">會使用 `<div />` 和 `<span />` [標記](xref:mvc/views/tag-helpers/intro)協助程式來啟用：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="1d0f6-255">用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-255">Client-side validation.</span></span>
  * <span data-ttu-id="1d0f6-256">驗證錯誤呈現。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-256">Validation error rendering.</span></span>

* <span data-ttu-id="1d0f6-257">產生下列 HTML：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="1d0f6-258">張貼不含名稱值的建立表單時，會顯示錯誤訊息「需要名稱欄位」。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="1d0f6-259">在表單上。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-259">on the form.</span></span> <span data-ttu-id="1d0f6-260">如果在用戶端上啟用 JavaScript，瀏覽器會顯示錯誤，而不會張貼至伺服器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="1d0f6-261">`[StringLength(10)]`屬性會 `data-val-length-max="10"` 在呈現的 HTML 上產生。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="1d0f6-262">`data-val-length-max`防止瀏覽器輸入超過指定的最大長度。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="1d0f6-263">如果使用[Fiddler](https://www.telerik.com/fiddler)之類的工具來編輯和重新播放文章：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="1d0f6-264">，其名稱超過10。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-264">With the name longer than 10.</span></span>
* <span data-ttu-id="1d0f6-265">錯誤訊息「功能變數名稱必須是最大長度為10的字串」。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="1d0f6-266">」錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-266">is returned.</span></span>

<span data-ttu-id="1d0f6-267">請考慮下列 `Movie` 模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="1d0f6-268">驗證屬性會指定要在其套用的模型屬性上強制執行的行為：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="1d0f6-269">`Required`和 `MinimumLength` 屬性（attribute）表示屬性（property）必須有值，但不會防止使用者輸入空白字元來滿足這種驗證。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="1d0f6-270">`RegularExpression` 屬性則用來限制可輸入的字元。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="1d0f6-271">在上述程式碼中，"Genre"：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="1d0f6-272">必須指使用字母。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-272">Must only use letters.</span></span>
  * <span data-ttu-id="1d0f6-273">第一個字母必須是大寫。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="1d0f6-274">不允許使用空格、數字和特殊字元。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="1d0f6-275">`RegularExpression` "Rating"：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="1d0f6-276">第一個字元必須為大寫字母。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="1d0f6-277">允許後續空格中的特殊字元和數位。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="1d0f6-278">"PG-13" 對分級而言有效，但不適用於 "Genre"。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="1d0f6-279">`Range` 屬性會將值限制在指定的範圍內。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="1d0f6-280">`StringLength`屬性會設定字串屬性的最大長度，並選擇性地設定其最小長度。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="1d0f6-281">實值型別 (如`decimal`、`int`、`float`、`DateTime`) 原本就是必要項目，而且不需要 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="1d0f6-282">模型的 [建立] 頁面會 `Movie` 顯示具有無效值的錯誤：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![有多個 jQuery 用戶端驗證錯誤的電影檢視表單](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="1d0f6-284">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="1d0f6-284">For more information, see:</span></span>

* [<span data-ttu-id="1d0f6-285">將驗證新增至電影應用程式</span><span class="sxs-lookup"><span data-stu-id="1d0f6-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="1d0f6-286">[ASP.NET Core 中的模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="1d0f6-287">使用 OnGet 處理常式後援來處理 HEAD 要求</span><span class="sxs-lookup"><span data-stu-id="1d0f6-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="1d0f6-288">`HEAD`要求可讓您抓取特定資源的標頭。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="1d0f6-289">不同於 `GET` 要求，`HEAD` 要求不會傳回回應主體。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="1d0f6-290">一般來說，會為 `HEAD` 要求建立及呼叫 `OnHead` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="1d0f6-291">Razor`OnGet`如果未 `OnHead` 定義任何處理程式，則頁面會回復為呼叫處理常式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="1d0f6-292">XSRF/CSRF 和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1d0f6-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="1d0f6-293">Razor頁面會受到[Antiforgery 驗證](xref:security/anti-request-forgery)的保護。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="1d0f6-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)會將 antiforgery TOKEN 插入 HTML 表單元素中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="1d0f6-295">搭配頁面使用版面配置、部分、範本和標籤協助程式 Razor</span><span class="sxs-lookup"><span data-stu-id="1d0f6-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="1d0f6-296">頁面會與視圖引擎的所有功能搭配使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="1d0f6-297">版面配置、部分、範本、標記協助程式、 *_ViewStart*和 *_ViewImports。 cshtml*的工作方式與傳統 Razor 視圖相同。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="1d0f6-298">可利用這些功能的一部分來整理這個頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="1d0f6-299">將[版面配置頁面](xref:mvc/views/layout)新增至 *Pages/Shared/_Layout.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="1d0f6-300">[版面](xref:mvc/views/layout)配置：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="1d0f6-301">控制每個頁面的版面配置 (除非頁面退出版面配置)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="1d0f6-302">匯入 HTML 結構，例如 JavaScript 和樣式表。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="1d0f6-303">頁面的內容 Razor 會在呼叫的位置呈現 `@RenderBody()` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="1d0f6-304">如需詳細資訊，請參閱[版面配置頁面](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="1d0f6-305">[版面配置](xref:mvc/views/layout#specifying-a-layout)屬性是在 *Pages/_ViewStart.cshtml* 中設定：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1d0f6-306">版面配置位於 *Pages/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="1d0f6-307">頁面會以階層方式尋找其他檢視 (版面配置、範本、部分)，從目前頁面的相同資料夾開始。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="1d0f6-308">*Pages/Shared*資料夾中的版面配置可以從 Razor [ *pages* ] 資料夾下的任何頁面使用。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="1d0f6-309">版面配置頁面應位於 *Pages/Shared* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="1d0f6-310">我們**不**建議您將配置檔案放入 *Views/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="1d0f6-311">*Views/Shared* 是 MVC 檢視模式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="1d0f6-312">Razor頁面的目的是要依賴資料夾階層，而不是路徑慣例。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="1d0f6-313">從頁面中查看搜尋 Razor 包含*Pages*資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="1d0f6-314">與 MVC 控制器和傳統視圖搭配使用的版面配置、範本和部分都 Razor *是可行*的。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="1d0f6-315">新增 *Pages/_ViewImports.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="1d0f6-316">本教學課程稍後會說明 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="1d0f6-317">`@addTagHelper` 指示詞會將[內建標記協助程式](xref:mvc/views/tag-helpers/builtin-th/Index)帶入 *Pages* 資料夾中的所有頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="1d0f6-318">在 `@namespace` 頁面上設定的指示詞：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="1d0f6-319">指示詞會 `@namespace` 設定頁面的命名空間。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="1d0f6-320">`@model` 指示詞不需要包含命名空間。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="1d0f6-321">當 `@namespace` 指示詞包含在 *_ViewImports.cshtml* 中時，指定的命名空間會在匯入 `@namespace` 指示詞的頁面中提供所產生之命名空間的前置詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="1d0f6-322">所產生命名空間的其餘部分 (後置字元部分) 是包含 *_ViewImports.cshtml* 的資料夾和包含頁面的資料夾之間，以句點分隔的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="1d0f6-323">例如，`PageModel` 類別 *Pages/Customers/Edit.cshtml.cs* 會明確地設定命名空間：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="1d0f6-324">*Pages/_ViewImports.cshtml* 檔案會設定下列命名空間：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-325">針對 [ *Pages/Customers/編輯. cshtml* ] 頁面產生的命名空間與 Razor 類別相同 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="1d0f6-326">`@namespace`*也適用于傳統的 Razor 視圖。*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="1d0f6-327">請考慮*Pages/Create. cshtml* view file：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="1d0f6-328">已更新的*Pages/Create. cshtml*視圖檔案，包含 *_ViewImports. cshtml*和先前的配置檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="1d0f6-329">在上述程式碼中， *_ViewImports. cshtml*已匯入命名空間和標籤協助程式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="1d0f6-330">設定檔案已匯入 JavaScript 檔案。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="1d0f6-331">[ Razor Pages 入門專案](#rpvs17)包含*pages/_ValidationScriptsPartial. cshtml*，這會連結用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="1d0f6-332">如需部分檢視的詳細資訊，請參閱 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="1d0f6-333">產生頁面 URL</span><span class="sxs-lookup"><span data-stu-id="1d0f6-333">URL generation for Pages</span></span>

<span data-ttu-id="1d0f6-334">前面出現過的 `Create` 頁面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="1d0f6-335">應用程式有下列檔案/資料夾結構：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="1d0f6-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-336">*/Pages*</span></span>

  * <span data-ttu-id="1d0f6-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="1d0f6-338">*隱私權. cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="1d0f6-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-339">*/Customers*</span></span>

    * <span data-ttu-id="1d0f6-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="1d0f6-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="1d0f6-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-342">*Index.cshtml*</span></span>

<span data-ttu-id="1d0f6-343">*Pages/customers/Create. cshtml*和*pages/customers/Edit. cshtml*頁面會在成功之後重新導向至*Pages/customers/Index. cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="1d0f6-344">此字串 `./Index` 是用來存取前一頁的相對頁面名稱。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="1d0f6-345">它可用來產生*Pages/Customers/Index. cshtml*頁面的 url。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="1d0f6-346">例如：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="1d0f6-347">絕對頁面名稱 `/Index` 是用來產生*Pages/Index. cshtml*頁面的 url。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="1d0f6-348">例如：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="1d0f6-349">頁面名稱是從根 */Pages* 資料夾到該頁面的路徑 (包括前置的 `/`，例如 `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="1d0f6-350">先前的 URL 產生範例提供了增強的選項和功能，透過硬式編碼 URL。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="1d0f6-351">URL 產生使用[路由](xref:mvc/controllers/routing)，可以根據路由在目的地路徑中定義的方式，產生並且編碼參數。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="1d0f6-352">產生頁面 URL 支援相關的名稱。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="1d0f6-353">下表顯示 `RedirectToPage` 在*Pages/Customers/Create. cshtml*中使用不同參數選取的索引頁。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="1d0f6-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="1d0f6-354">RedirectToPage(x)</span></span>| <span data-ttu-id="1d0f6-355">頁面</span><span class="sxs-lookup"><span data-stu-id="1d0f6-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1d0f6-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="1d0f6-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="1d0f6-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-357">*Pages/Index*</span></span> |
| <span data-ttu-id="1d0f6-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="1d0f6-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="1d0f6-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="1d0f6-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="1d0f6-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="1d0f6-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-361">*Pages/Index*</span></span> |
| <span data-ttu-id="1d0f6-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="1d0f6-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="1d0f6-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="1d0f6-364">`RedirectToPage("Index")`、 `RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是*相對名稱*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="1d0f6-365">`RedirectToPage` 參數「結合」\*\* 了目前頁面的路徑，以計算目的地頁面的名稱。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="1d0f6-366">相對名稱連結在以複雜結構建置網站時很有用。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="1d0f6-367">當使用相對名稱來連結資料夾中的頁面時：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="1d0f6-368">重新命名資料夾並不會中斷相對連結。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="1d0f6-369">連結不會中斷，因為它們不包含資料夾名稱。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="1d0f6-370">若要重新導向到不同[區域](xref:mvc/controllers/areas)中的頁面，請指定區域：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="1d0f6-371">如需詳細資訊，請參閱 <xref:mvc/controllers/areas> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="1d0f6-372">ViewData 屬性</span><span class="sxs-lookup"><span data-stu-id="1d0f6-372">ViewData attribute</span></span>

<span data-ttu-id="1d0f6-373">您可以使用將資料傳遞至頁面 <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="1d0f6-374">具有屬性的屬性 `[ViewData]` 會將其值儲存並從載入 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="1d0f6-375">在下列範例中，會將 `AboutModel` `[ViewData]` 屬性套用至 `Title` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="1d0f6-376">在 [關於] 頁面上，存取 `Title` 屬性作為模型屬性：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="1d0f6-377">在此配置中，標題會從 ViewData 字典中讀取：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="1d0f6-378">TempData</span><span class="sxs-lookup"><span data-stu-id="1d0f6-378">TempData</span></span>

<span data-ttu-id="1d0f6-379">ASP.NET Core 會公開 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="1d0f6-380">這個屬性會儲存資料，直到讀取為止。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-380">This property stores data until it's read.</span></span> <span data-ttu-id="1d0f6-381"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> 和 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> 方法可以用來檢查資料，不用刪除。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="1d0f6-382">`TempData`當需要多個單一要求的資料時，對重新導向很有用。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="1d0f6-383">下列程式碼會設定使用 `TempData` 的 `Message` 值：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="1d0f6-384">*Pages/Customers/Index.cshtml* 檔案中的下列標記會顯示使用 `TempData` 的 `Message` 值。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="1d0f6-385">*Pages/Customers/Index.cshtml.cs* 頁面模型會將 `[TempData]` 屬性 (attribute) 套用到 `Message` 屬性 (property)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="1d0f6-386">如需詳細資訊，請參閱[TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="1d0f6-387">每頁面有多個處理常式</span><span class="sxs-lookup"><span data-stu-id="1d0f6-387">Multiple handlers per page</span></span>

<span data-ttu-id="1d0f6-388">下列頁面會使用 `asp-page-handler` 標記協助程式為兩個處理常式產生標記：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="1d0f6-389">上例中的表單有兩個提交按鈕，每一個都使用 `FormActionTagHelper` 提交至不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="1d0f6-390">`asp-page-handler` 屬性附隨於 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="1d0f6-391">`asp-page-handler` 產生的 URL 會提交至頁面所定義的每一個處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="1d0f6-392">因為範例連結至目前的頁面，所以未指定 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="1d0f6-393">頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-393">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="1d0f6-394">上述程式碼使用「具名的處理常式方法」\*\*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="1d0f6-395">具名的處理常式方法的建立方式是採用名稱中在 `On<HTTP Verb>` 後面、`Async` 之前 (如有) 的文字。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="1d0f6-396">在上例中，頁面方法是 OnPost**JoinList**Async 和 OnPost**JoinListUC**Async。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="1d0f6-397">移除 *OnPost* 和 *Async*，處理常式名稱就是 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="1d0f6-398">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="1d0f6-399">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="1d0f6-400">自訂路由</span><span class="sxs-lookup"><span data-stu-id="1d0f6-400">Custom routes</span></span>

<span data-ttu-id="1d0f6-401">使用 `@page` 指示詞，可以：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="1d0f6-402">指定頁面的自訂路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-402">Specify a custom route to a page.</span></span> <span data-ttu-id="1d0f6-403">例如，[關於] 頁面的路由可使用 `@page "/Some/Other/Path"` 設為 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="1d0f6-404">將區段附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-404">Append segments to a page's default route.</span></span> <span data-ttu-id="1d0f6-405">例如，使用 `@page "item"` 可將 "item" 區段新增到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="1d0f6-406">將參數附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="1d0f6-407">例如，具有 `@page "{id}"` 的頁面可要求識別碼參數 `id`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="1d0f6-408">支援在路徑開頭以波狀符號 (`~`) 指定根相對路徑。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="1d0f6-409">例如，`@page "~/Some/Other/Path"` 與 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="1d0f6-410">如果您不喜歡 URL 中的查詢字串 `?handler=JoinList` ，請變更路由，將處理常式名稱放在 url 的路徑部分。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="1d0f6-411">藉由在指示詞後面加上雙引號括住的路由範本，可以自訂路由 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-412">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="1d0f6-413">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="1d0f6-414">跟在 `handler` 後面的 `?` 表示路由參數為選擇性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="1d0f6-415">先進的設定和設定</span><span class="sxs-lookup"><span data-stu-id="1d0f6-415">Advanced configuration and settings</span></span>

<span data-ttu-id="1d0f6-416">大部分的應用程式都不需要下列章節中的設定和設定。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="1d0f6-417">若要設定 advanced 選項，請使用設定的多載 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> ：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="1d0f6-418">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> 來設定頁面的根目錄，或加入頁面的應用程式模型慣例。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="1d0f6-419">如需慣例的詳細資訊，請參閱[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="1d0f6-420">若要先行編譯視圖，請參閱[ Razor view 編譯](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="1d0f6-421">指定 Razor 頁面位於內容根目錄</span><span class="sxs-lookup"><span data-stu-id="1d0f6-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="1d0f6-422">根據預設， Razor 頁面會以根方式在 */Pages*目錄中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="1d0f6-423">新增 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> 以指定您的 Razor 頁面位於應用程式的[內容根](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) ：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="1d0f6-424">指定 Razor 頁面位於自訂根目錄</span><span class="sxs-lookup"><span data-stu-id="1d0f6-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="1d0f6-425">新增 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> 以指定 Razor 頁面位於應用程式中的自訂根目錄， (提供相對路徑) ：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1d0f6-426">其他資源</span><span class="sxs-lookup"><span data-stu-id="1d0f6-426">Additional resources</span></span>

* <span data-ttu-id="1d0f6-427">請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)，這會在此簡介中建立。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="1d0f6-428">授權屬性和 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="1d0f6-428">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="1d0f6-429">下載或查看範例程式碼</span><span class="sxs-lookup"><span data-stu-id="1d0f6-429">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d0f6-430">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="1d0f6-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="1d0f6-431">Razor頁面是 ASP.NET Core MVC 的新層面，可讓撰寫以頁面為焦點的案例更輕鬆且更具生產力。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="1d0f6-432">如果您在尋找使用模型檢視控制器方法的教學課程，請參閱[開始使用 ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="1d0f6-433">本檔提供 Razor 頁面簡介。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="1d0f6-434">它不是逐步教學課程。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="1d0f6-435">如果您發現某些區段太先進，請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="1d0f6-436">如需 ASP.NET Core 的概觀，請參閱[ASP.NET Core 簡介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1d0f6-437">必要條件</span><span class="sxs-lookup"><span data-stu-id="1d0f6-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d0f6-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d0f6-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d0f6-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d0f6-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d0f6-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1d0f6-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="1d0f6-441">建立 Razor 頁面專案</span><span class="sxs-lookup"><span data-stu-id="1d0f6-441">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d0f6-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d0f6-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1d0f6-443">如需如何建立頁面專案的詳細指示，請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start) Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d0f6-444">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1d0f6-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1d0f6-445">從命令列執行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="1d0f6-446">從 Visual Studio for Mac 開啟已產生的 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d0f6-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d0f6-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1d0f6-448">從命令列執行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="1d0f6-449">Razor頁面</span><span class="sxs-lookup"><span data-stu-id="1d0f6-449">Razor Pages</span></span>

<span data-ttu-id="1d0f6-450">Razor頁面已在*Startup.cs*中啟用：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="1d0f6-451">請考慮使用基本頁面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="1d0f6-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="1d0f6-452">上述程式碼看起來很像是在具有控制器和 views 的 ASP.NET Core 應用程式中使用的[ Razor 視圖](xref:tutorials/first-mvc-app/adding-view)檔案。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="1d0f6-453">讓它不同的是 `@page` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="1d0f6-454">`@page` 會將檔案轉換成 MVC 動作，這表示它會直接處理要求，不用透過控制器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="1d0f6-455">`@page`必須是頁面上的第一個指示詞 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="1d0f6-456">`@page`會影響其他結構的行為 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="1d0f6-457">使用`PageModel`類別的類似頁面，顯示於下列兩個檔案中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="1d0f6-458">*Pages/Index2.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="1d0f6-459">*Pages/Index2.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="1d0f6-460">依照慣例， `PageModel` 類別檔案的名稱與分頁檔相同， Razor 附加 *.cs* 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="1d0f6-461">例如，前一 Razor 頁是*Pages/Index2*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="1d0f6-462">包含 `PageModel` 類別的檔案名為 *Pages/Index2.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="1d0f6-463">頁面的 URL 路徑關聯是由頁面在檔案系統中的位置決定。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="1d0f6-464">下表顯示 Razor 頁面路徑和相符的 URL：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="1d0f6-465">檔案名稱和路徑</span><span class="sxs-lookup"><span data-stu-id="1d0f6-465">File name and path</span></span>               | <span data-ttu-id="1d0f6-466">比對 URL</span><span class="sxs-lookup"><span data-stu-id="1d0f6-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1d0f6-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="1d0f6-468">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="1d0f6-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="1d0f6-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="1d0f6-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="1d0f6-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="1d0f6-472">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="1d0f6-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="1d0f6-473">注意：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-473">Notes:</span></span>

* <span data-ttu-id="1d0f6-474">執行時間預設會 Razor 在*pages*資料夾中尋找分頁檔案。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="1d0f6-475">`Index` 是 URL 未包含頁面時的預設頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="1d0f6-476">撰寫基本表單</span><span class="sxs-lookup"><span data-stu-id="1d0f6-476">Write a basic form</span></span>

<span data-ttu-id="1d0f6-477">Razor頁面的設計目的是要在建立應用程式時，讓與網頁瀏覽器搭配使用的常見模式變得容易執行。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="1d0f6-478">[模型](xref:mvc/models/model-binding)系結、[標記](xref:mvc/views/tag-helpers/intro)協助程式和 HTML 協助程式都*只*會使用頁面類別中定義的屬性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="1d0f6-479">`Contact` 模型請考慮實作基本的「與我們連絡」格式頁面：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="1d0f6-480">本文件中的範例，會在 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 檔案中初始化 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="1d0f6-481">資料模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-481">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="1d0f6-482">DB 內容：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-482">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="1d0f6-483">*Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="1d0f6-484">*Pages/Create.cshtml.cs* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="1d0f6-485">依照慣例，`PageModel` 類別稱之為 `<PageName>Model`，與頁面位於相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="1d0f6-486">`PageModel` 類別可以分離頁面邏輯與頁面展示。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="1d0f6-487">此類別會定義頁面的處理常式，以處理傳送至頁面的要求與用於轉譯頁面的資料。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="1d0f6-488">這種分隔允許：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-488">This separation allows:</span></span>

* <span data-ttu-id="1d0f6-489">透過相依性[插入](xref:fundamentals/dependency-injection)來管理頁面相依性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="1d0f6-490">對頁面進行[單元測試](xref:test/razor-pages-tests)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="1d0f6-491">在 `POST` 要求上執行的頁面具有 「處理常式方法」`OnPostAsync` \*\* (當使用者張貼表單時)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="1d0f6-492">您可以新增任何 HTTP 指令動詞的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="1d0f6-493">最常見的處理常式包括：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-493">The most common handlers are:</span></span>

* <span data-ttu-id="1d0f6-494">`OnGet`，初始化頁所需要的狀態。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="1d0f6-495">[OnGet](#OnGet) 範例。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="1d0f6-496">`OnPost`，處理表單提交作業。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="1d0f6-497">`Async` 命名尾碼是選擇性的，但依照慣例通常用於非同步函式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="1d0f6-498">上述程式碼通常用於 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="1d0f6-499">如果您熟悉如何使用控制器和 views 來 ASP.NET 應用程式：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="1d0f6-500">`OnPostAsync`上述範例中的程式碼看起來類似一般的控制器程式碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="1d0f6-501">大部分的 MVC 基本類型（例如[模型](xref:mvc/models/model-binding)系結、[驗證](xref:mvc/models/validation)、[驗證](xref:mvc/models/validation)和動作結果）都是共用的。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="1d0f6-502">前一個 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="1d0f6-503">`OnPostAsync` 的基本流程：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="1d0f6-504">檢查驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-504">Check for validation errors.</span></span>

* <span data-ttu-id="1d0f6-505">如果沒有任何錯誤，會儲存資料並重新導向。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="1d0f6-506">如果有錯誤，會再次顯示有驗證訊息的頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="1d0f6-507">用戶端驗證和傳統的 ASP.NET Core MVC 應用程式完全相同。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="1d0f6-508">在許多情況下，會在用戶端上偵測到驗證錯誤，且永遠不會提交到伺服器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="1d0f6-509">成功輸入資料後，`OnPostAsync` 處理常式方法會呼叫 `RedirectToPage` 協助程式方法，傳回 `RedirectToPageResult` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="1d0f6-510">`RedirectToPage` 是新的動作結果，類似於 `RedirectToAction` 或 `RedirectToRoute`，但會針對頁面自訂。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="1d0f6-511">在上述範例中，它會重新導向至根索引頁面 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="1d0f6-512">[產生頁面 URL](#url_gen)一節會詳細說明 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="1d0f6-513">當提交的表單有驗證錯誤時 (傳遞至伺服器)，`OnPostAsync` 處理常式方法會呼叫 `Page` 協助程式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="1d0f6-514">`Page` 會傳回 `PageResult` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="1d0f6-515">傳回 `Page` 類似於控制站中的動作傳回 `View`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="1d0f6-516">`PageResult`這是處理常式方法的預設傳回型別。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="1d0f6-517">傳回 `void` 的處理常式方法會呈現頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="1d0f6-518">`Customer` 屬性 (property) 使用 `[BindProperty]` 屬性 (attribute) 加入模型繫結。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="1d0f6-519">Razor根據預設，頁面只會系結屬性與非 `GET` 動詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="1d0f6-520">繫結至屬性可以減少您必須撰寫的程式碼數量。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="1d0f6-521">透過使用相同的屬性呈現表單欄位 (`<input asp-for="Customer.Name">`) 並接受輸入，繫結可以減少程式碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="1d0f6-522">首頁 (*Index.cshtml*)：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="1d0f6-523">已建立關聯的 `PageModel` 類別 (*Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="1d0f6-524">*Index.cshtml* 檔案包含下列標記可為每個連絡人建立編輯連結：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="1d0f6-525">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>`[錨點](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)標籤協助程式使用 `asp-route-{value}` 屬性來產生 [編輯] 頁面的連結。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="1d0f6-526">該連結包含路由資料和連絡人識別碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="1d0f6-527">例如，`https://localhost:5001/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="1d0f6-528">[標記](xref:mvc/views/tag-helpers/intro)協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="1d0f6-529">標記協助程式由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 啟用</span><span class="sxs-lookup"><span data-stu-id="1d0f6-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="1d0f6-530">*Pages/Edit.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-531">第一行包含 `@page "{id:int}"` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="1d0f6-532">路由條件約束 `"{id:int}"` 通知頁面接受包含 `int` 路由資料的頁面要求。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="1d0f6-533">如果頁面要求不包含可以轉換成 `int` 的路由資料，執行階段會傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="1d0f6-534">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="1d0f6-535">*Pages/Edit.cshtml.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="1d0f6-536">*Index.cshtml* 檔案也包含能夠為每個客戶連絡人建立刪除按鈕的標記：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="1d0f6-537">使用 HTML 轉譯刪除按鈕時，其 `formaction` 會包含下列項目的參數：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="1d0f6-538">`asp-route-id` 屬性指定的客戶連絡人識別碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="1d0f6-539">`asp-page-handler` 屬性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="1d0f6-540">以下是轉譯的刪除按鈕範例，內含客戶連絡人識別碼 `1`：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="1d0f6-541">選取按鈕時，表單 `POST` 要求會傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="1d0f6-542">依照慣例，會依據配置 `OnPost[handler]Async`，按 `handler` 參數的值來選取處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="1d0f6-543">在此範例中，因為 `handler` 為 `delete`，所以會使用 `OnPostDeleteAsync` 處理常式方法來處理 `POST` 要求。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="1d0f6-544">若 `asp-page-handler` 設為其他值 (例如 `remove`)，則會選取名為 `OnPostRemoveAsync` 的處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="1d0f6-545">下列程式碼會顯示 `OnPostDeleteAsync` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="1d0f6-546">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="1d0f6-547">接受查詢字串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="1d0f6-548">如果包含路由條件約束的*Index. cshtml*頁面指示詞 `"{id:int?}"` ，則 `id` 會來自路由資料。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="1d0f6-549">的路由資料 `id` 是在 URI 中指定，例如 `https://localhost:5001/Customers/2` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="1d0f6-550">使用 `FindAsync` 在資料庫中查詢客戶連絡人。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="1d0f6-551">若找到客戶連絡人，會從客戶連絡人清單中予以移除。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="1d0f6-552">資料庫隨即更新。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-552">The database is updated.</span></span>
* <span data-ttu-id="1d0f6-553">呼叫 `RedirectToPage` 以重新導向至根索引頁 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="1d0f6-554">將頁面屬性標示為必要</span><span class="sxs-lookup"><span data-stu-id="1d0f6-554">Mark page properties as required</span></span>

<span data-ttu-id="1d0f6-555">上的屬性 `PageModel` 可以標記為[必要](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)的屬性：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="1d0f6-556">如需詳細資訊，請參閱[模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="1d0f6-557">使用 OnGet 處理常式後援來處理 HEAD 要求</span><span class="sxs-lookup"><span data-stu-id="1d0f6-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="1d0f6-558">`HEAD` 要求可讓您擷取特定資源的標頭。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="1d0f6-559">不同於 `GET` 要求，`HEAD` 要求不會傳回回應主體。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="1d0f6-560">一般來說，會為 `HEAD` 要求建立及呼叫 `OnHead` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="1d0f6-561">在 ASP.NET Core 2.1 或更新版本中， Razor `OnGet` 如果未定義任何處理程式，頁面就會回復為呼叫處理常式 `OnHead` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="1d0f6-562">這個行為藉由在 `Startup.ConfigureServices` 中呼叫 [SetCompatibilityVersion](xref:mvc/compatibility-version) 來啟用：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="1d0f6-563">預設範本會在 ASP.NET Core 2.1 和 2.2 中產生 `SetCompatibilityVersion` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="1d0f6-564">`SetCompatibilityVersion`將 Pages 選項有效地設定 Razor `AllowMappingHeadRequestsToGetHandler` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="1d0f6-565">您可以明確選擇「特定」\*\* 行為，而不必透過 `SetCompatibilityVersion` 選擇所有行為。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="1d0f6-566">下列程式碼會選擇讓 `HEAD` 要求對應到 `OnGet` 處理常式：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="1d0f6-567">XSRF/CSRF 和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1d0f6-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="1d0f6-568">您不必撰寫任何[防偽驗證](xref:security/anti-request-forgery)程式碼。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="1d0f6-569">Antiforgery 權杖的產生和驗證會自動包含在 Razor 頁面中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="1d0f6-570">搭配頁面使用版面配置、部分、範本和標籤協助程式 Razor</span><span class="sxs-lookup"><span data-stu-id="1d0f6-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="1d0f6-571">頁面會與視圖引擎的所有功能搭配使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="1d0f6-572">版面配置、部分、範本、標記協助程式、 *_ViewStart* *_ViewImports cshtml*的工作方式，與傳統 Razor 視圖相同。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="1d0f6-573">可利用這些功能的一部分來整理這個頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="1d0f6-574">將[版面配置頁面](xref:mvc/views/layout)新增至 *Pages/Shared/_Layout.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="1d0f6-575">[版面](xref:mvc/views/layout)配置：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="1d0f6-576">控制每個頁面的版面配置 (除非頁面退出版面配置)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="1d0f6-577">匯入 HTML 結構，例如 JavaScript 和樣式表。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="1d0f6-578">如需詳細資訊，請參閱[版面配置頁面](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="1d0f6-579">[版面配置](xref:mvc/views/layout#specifying-a-layout)屬性是在 *Pages/_ViewStart.cshtml* 中設定：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="1d0f6-580">版面配置位於 *Pages/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="1d0f6-581">頁面會以階層方式尋找其他檢視 (版面配置、範本、部分)，從目前頁面的相同資料夾開始。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="1d0f6-582">*Pages/Shared*資料夾中的版面配置可以從 Razor [ *pages* ] 資料夾下的任何頁面使用。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="1d0f6-583">版面配置頁面應位於 *Pages/Shared* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="1d0f6-584">我們**不**建議您將配置檔案放入 *Views/Shared* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="1d0f6-585">*Views/Shared* 是 MVC 檢視模式。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="1d0f6-586">Razor頁面的目的是要依賴資料夾階層，而不是路徑慣例。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="1d0f6-587">從頁面中查看搜尋 Razor 包含*Pages*資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="1d0f6-588">您在 MVC 控制器和傳統視圖中使用的配置、範本和部分 Razor 都*是可行*的。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="1d0f6-589">新增 *Pages/_ViewImports.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="1d0f6-590">本教學課程稍後會說明 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="1d0f6-591">`@addTagHelper` 指示詞會將[內建標記協助程式](xref:mvc/views/tag-helpers/builtin-th/Index)帶入 *Pages* 資料夾中的所有頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="1d0f6-592">在頁面上明確使用 `@namespace` 指示詞時：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="1d0f6-593">指示詞會設定頁面的命名空間。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="1d0f6-594">`@model` 指示詞不需要包含命名空間。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="1d0f6-595">當 `@namespace` 指示詞包含在 *_ViewImports.cshtml* 中時，指定的命名空間會在匯入 `@namespace` 指示詞的頁面中提供所產生之命名空間的前置詞。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="1d0f6-596">所產生命名空間的其餘部分 (後置字元部分) 是包含 *_ViewImports.cshtml* 的資料夾和包含頁面的資料夾之間，以句點分隔的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="1d0f6-597">例如，`PageModel` 類別 *Pages/Customers/Edit.cshtml.cs* 會明確地設定命名空間：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="1d0f6-598">*Pages/_ViewImports.cshtml* 檔案會設定下列命名空間：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-599">針對 [ *Pages/Customers/編輯. cshtml* ] 頁面產生的命名空間與 Razor 類別相同 `PageModel` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="1d0f6-600">`@namespace`*也適用于傳統的 Razor 視圖。*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="1d0f6-601">原始的 *Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="1d0f6-602">更新的 *Pages/Create.cshtml* 檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="1d0f6-603">[ Razor Pages 入門專案](#rpvs17)包含*pages/_ValidationScriptsPartial. cshtml*，這會連結用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="1d0f6-604">如需部分檢視的詳細資訊，請參閱 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="1d0f6-605">產生頁面 URL</span><span class="sxs-lookup"><span data-stu-id="1d0f6-605">URL generation for Pages</span></span>

<span data-ttu-id="1d0f6-606">前面出現過的 `Create` 頁面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="1d0f6-607">應用程式有下列檔案/資料夾結構：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="1d0f6-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-608">*/Pages*</span></span>

  * <span data-ttu-id="1d0f6-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="1d0f6-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-610">*/Customers*</span></span>

    * <span data-ttu-id="1d0f6-611">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="1d0f6-612">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="1d0f6-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-613">*Index.cshtml*</span></span>

<span data-ttu-id="1d0f6-614">*Pages/Customers/Create.cshtml* 和 *Pages/Customers/Edit.cshtml* 頁面在成功後會重新導向至 *Pages/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="1d0f6-615">字串 `/Index` 為 URI 的一部分，可存取前一個頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="1d0f6-616">字串 `/Index` 可以用來產生 *Pages/Index.cshtml* 頁面的 URI。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="1d0f6-617">例如：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="1d0f6-618">頁面名稱是從根 */Pages* 資料夾到該頁面的路徑 (包括前置的 `/`，例如 `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="1d0f6-619">上述 URL 產生範例，透過硬式編碼的 URL 提供更加優異的選項與功能。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="1d0f6-620">URL 產生使用[路由](xref:mvc/controllers/routing)，可以根據路由在目的地路徑中定義的方式，產生並且編碼參數。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="1d0f6-621">產生頁面 URL 支援相關的名稱。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="1d0f6-622">下表顯示從 *Pages/Customers/Create.cshtml* 以不同的 `RedirectToPage` 參數選取的索引頁：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="1d0f6-623">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="1d0f6-623">RedirectToPage(x)</span></span>| <span data-ttu-id="1d0f6-624">頁面</span><span class="sxs-lookup"><span data-stu-id="1d0f6-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="1d0f6-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="1d0f6-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="1d0f6-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-626">*Pages/Index*</span></span> |
| <span data-ttu-id="1d0f6-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="1d0f6-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="1d0f6-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="1d0f6-629">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="1d0f6-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="1d0f6-630">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-630">*Pages/Index*</span></span> |
| <span data-ttu-id="1d0f6-631">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="1d0f6-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="1d0f6-632">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="1d0f6-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="1d0f6-633">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是「相對名稱」\*\*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="1d0f6-634">`RedirectToPage` 參數「結合」\*\* 了目前頁面的路徑，以計算目的地頁面的名稱。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="1d0f6-635">相對名稱連結在以複雜結構建置網站時很有用。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="1d0f6-636">如果您使用相對名稱連結資料夾中的頁面，您可以重新命名該資料夾。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="1d0f6-637">所有連結仍可運作 (因為它們不包含資料夾名稱)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="1d0f6-638">若要重新導向到不同[區域](xref:mvc/controllers/areas)中的頁面，請指定區域：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="1d0f6-639">如需詳細資訊，請參閱<xref:mvc/controllers/areas>。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="1d0f6-640">ViewData 屬性</span><span class="sxs-lookup"><span data-stu-id="1d0f6-640">ViewData attribute</span></span>

<span data-ttu-id="1d0f6-641">資料可以傳遞至具有 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) 的頁面。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="1d0f6-642">具有屬性的控制器或頁面模型上的屬性， Razor `[ViewData]` 其值會從[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)儲存並載入。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-642">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="1d0f6-643">在下列範例中， `AboutModel` 包含以 `Title` 標記的屬性 `[ViewData]` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="1d0f6-644">`Title` 屬性會設定為 [關於] 頁面的標題：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-644">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="1d0f6-645">在 [關於] 頁面上，存取 `Title` 屬性作為模型屬性：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="1d0f6-646">在此配置中，標題會從 ViewData 字典中讀取：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="1d0f6-647">TempData</span><span class="sxs-lookup"><span data-stu-id="1d0f6-647">TempData</span></span>

<span data-ttu-id="1d0f6-648">ASP.NET Core 公開[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)上的 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) 屬性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="1d0f6-649">這個屬性會儲存資料，直到讀取為止。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-649">This property stores data until it's read.</span></span> <span data-ttu-id="1d0f6-650">`Keep` 和 `Peek` 方法可以用來檢查資料，不用刪除。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="1d0f6-651">當有多個要求需要資料時，`TempData` 對重新導向很有幫助。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="1d0f6-652">下列程式碼會設定使用 `TempData` 的 `Message` 值：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="1d0f6-653">*Pages/Customers/Index.cshtml* 檔案中的下列標記會顯示使用 `TempData` 的 `Message` 值。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="1d0f6-654">*Pages/Customers/Index.cshtml.cs* 頁面模型會將 `[TempData]` 屬性 (attribute) 套用到 `Message` 屬性 (property)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="1d0f6-655">如需詳細資訊，請參閱 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="1d0f6-656">每頁面有多個處理常式</span><span class="sxs-lookup"><span data-stu-id="1d0f6-656">Multiple handlers per page</span></span>

<span data-ttu-id="1d0f6-657">下列頁面會使用 `asp-page-handler` 標記協助程式為兩個處理常式產生標記：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="1d0f6-658">上例中的表單有兩個提交按鈕，每一個都使用 `FormActionTagHelper` 提交至不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="1d0f6-659">`asp-page-handler` 屬性附隨於 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="1d0f6-660">`asp-page-handler` 產生的 URL 會提交至頁面所定義的每一個處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="1d0f6-661">因為範例連結至目前的頁面，所以未指定 `asp-page`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="1d0f6-662">頁面模型：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-662">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="1d0f6-663">上述程式碼使用「具名的處理常式方法」\*\*。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="1d0f6-664">具名的處理常式方法的建立方式是採用名稱中在 `On<HTTP Verb>` 後面、`Async` 之前 (如有) 的文字。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="1d0f6-665">在上例中，頁面方法是 OnPost**JoinList**Async 和 OnPost**JoinListUC**Async。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="1d0f6-666">移除 *OnPost* 和 *Async*，處理常式名稱就是 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="1d0f6-667">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="1d0f6-668">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="1d0f6-669">自訂路由</span><span class="sxs-lookup"><span data-stu-id="1d0f6-669">Custom routes</span></span>

<span data-ttu-id="1d0f6-670">使用 `@page` 指示詞，可以：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="1d0f6-671">指定頁面的自訂路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-671">Specify a custom route to a page.</span></span> <span data-ttu-id="1d0f6-672">例如，[關於] 頁面的路由可使用 `@page "/Some/Other/Path"` 設為 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="1d0f6-673">將區段附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-673">Append segments to a page's default route.</span></span> <span data-ttu-id="1d0f6-674">例如，使用 `@page "item"` 可將 "item" 區段新增到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="1d0f6-675">將參數附加到頁面的預設路由。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="1d0f6-676">例如，具有 `@page "{id}"` 的頁面可要求識別碼參數 `id`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="1d0f6-677">支援在路徑開頭以波狀符號 (`~`) 指定根相對路徑。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="1d0f6-678">例如，`@page "~/Some/Other/Path"` 與 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="1d0f6-679">如果您不喜歡 URL 中的查詢字串 `?handler=JoinList` ，請變更路由，將處理常式名稱放在 url 的路徑部分。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="1d0f6-680">藉由在指示詞後面加上雙引號括住的路由範本，可以自訂路由 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="1d0f6-681">使用上述程式碼，提交至 `OnPostJoinListAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="1d0f6-682">提交至 `OnPostJoinListUCAsync` 的 URL 路徑是 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="1d0f6-683">跟在 `handler` 後面的 `?` 表示路由參數為選擇性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="1d0f6-684">組態與設定</span><span class="sxs-lookup"><span data-stu-id="1d0f6-684">Configuration and settings</span></span>

<span data-ttu-id="1d0f6-685">若要設定進階選項，請在 MVC 產生器上使用擴充方法 `AddRazorPagesOptions`：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-685">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="1d0f6-686">目前可以使用 `RazorPagesOptions` 設定頁面的根目錄，或新增頁面的應用程式模型慣例。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-686">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="1d0f6-687">我們將在未來以這種方式獲得更多的擴充性。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="1d0f6-688">若要先行編譯視圖，請參閱[ Razor view 編譯](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-688">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="1d0f6-689">[下載或檢視範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="1d0f6-690">請參閱[開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)，這會在此簡介中建立。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-690">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="1d0f6-691">指定 Razor 頁面位於內容根目錄</span><span class="sxs-lookup"><span data-stu-id="1d0f6-691">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="1d0f6-692">根據預設， Razor 頁面會以根方式在 */Pages*目錄中。</span><span class="sxs-lookup"><span data-stu-id="1d0f6-692">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="1d0f6-693">將[ Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot)新增至[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ，以指定您的 Razor 頁面位於應用程式的[內容根目錄](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) ：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-693">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="1d0f6-694">指定 Razor 頁面位於自訂根目錄</span><span class="sxs-lookup"><span data-stu-id="1d0f6-694">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="1d0f6-695">將[ Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot)新增至[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ，以指定您的 Razor 頁面位於應用程式中的自訂根目錄， (提供相對路徑) ：</span><span class="sxs-lookup"><span data-stu-id="1d0f6-695">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="1d0f6-696">其他資源</span><span class="sxs-lookup"><span data-stu-id="1d0f6-696">Additional resources</span></span>

* [<span data-ttu-id="1d0f6-697">授權屬性和 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="1d0f6-697">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
