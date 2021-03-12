---
title: ASP.NET Core 中的區域
author: rick-anderson
description: 了解其為 ASP.NET MVC 功能的區域，如何用來將相關功能組織成群組，作為個別命名空間 (適用於路由) 和資料夾結構 (適用於檢視)。
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: f3bd2d3eac97e0fd64d1e3a98a9d1750f7a607a8
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588369"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="aa380-103">ASP.NET Core 中的區域</span><span class="sxs-lookup"><span data-stu-id="aa380-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="aa380-104">[Dhananjay Kumar](https://twitter.com/debug_mode)與[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aa380-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa380-105">區域是一種 ASP.NET 功能，用來將相關功能組織成個別的群組：</span><span class="sxs-lookup"><span data-stu-id="aa380-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="aa380-106">路由的命名空間。</span><span class="sxs-lookup"><span data-stu-id="aa380-106">Namespace for routing.</span></span>
* <span data-ttu-id="aa380-107">Views 和 Pages 的資料夾結構 Razor 。</span><span class="sxs-lookup"><span data-stu-id="aa380-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="aa380-108">使用區域會藉由將另一個路由參數、 `area` 、新增至 `controller` 和 `action` 或 Razor 頁面，來建立路由用途的階層 `page` 。</span><span class="sxs-lookup"><span data-stu-id="aa380-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="aa380-109">區域可讓您將 ASP.NET Core Web 應用程式分割成較小的功能群組，每個群組都有自己的一組 Razor 頁面、控制器、視圖和模型。</span><span class="sxs-lookup"><span data-stu-id="aa380-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="aa380-110">一個區域基本上是應用程式內的一個結構。</span><span class="sxs-lookup"><span data-stu-id="aa380-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="aa380-111">在 ASP.NET Core Web 專案中，Pages、模型、控制器和檢視等邏輯元件都會保留在不同的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aa380-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="aa380-112">ASP.NET Core 執行階段會使用命名慣例來建立這些元件之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="aa380-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="aa380-113">針對大型應用程式，將應用程式分割成個別高功能層級區域可能較有利。</span><span class="sxs-lookup"><span data-stu-id="aa380-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="aa380-114">舉例來說，一個電子商務應用程式可具有多個業務單位，例如結帳、計費和搜尋。</span><span class="sxs-lookup"><span data-stu-id="aa380-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="aa380-115">每個單位都有自己的區域，以包含視圖、控制器、 Razor 頁面和模型。</span><span class="sxs-lookup"><span data-stu-id="aa380-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="aa380-116">處於下列情況時，請考慮在專案中使用區域：</span><span class="sxs-lookup"><span data-stu-id="aa380-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="aa380-117">應用程式是由可以邏輯方式區隔的多個高階功能性元件所組成的。</span><span class="sxs-lookup"><span data-stu-id="aa380-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="aa380-118">您想要分割應用程式，讓每個功能區域都可獨立運作。</span><span class="sxs-lookup"><span data-stu-id="aa380-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="aa380-119">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aa380-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="aa380-120">下載範例提供基本的應用程式來測試區域。</span><span class="sxs-lookup"><span data-stu-id="aa380-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="aa380-121">如果您使用的是 Razor 頁面，請參閱本檔中 [含有 Razor 頁面的區域](#areas-with-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="aa380-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="aa380-122">適用於控制器與檢視的區域</span><span class="sxs-lookup"><span data-stu-id="aa380-122">Areas for controllers with views</span></span>

<span data-ttu-id="aa380-123">使用區域、控制器及檢視的典型 ASP.NET Core Web 應用程式包含下列項目：</span><span class="sxs-lookup"><span data-stu-id="aa380-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="aa380-124">一個[區域資料夾結構](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="aa380-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="aa380-125">具有屬性的控制器， [`[Area]`](#attribute) 可讓控制器與區域產生關聯：</span><span class="sxs-lookup"><span data-stu-id="aa380-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="aa380-126">[新增至啟動的區域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="aa380-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="aa380-127">區域資料夾結構</span><span class="sxs-lookup"><span data-stu-id="aa380-127">Area folder structure</span></span>

<span data-ttu-id="aa380-128">假設應用程式具有兩個邏輯群組：「產品」和「服務」。</span><span class="sxs-lookup"><span data-stu-id="aa380-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="aa380-129">使用區域，資料夾結構應該如下：</span><span class="sxs-lookup"><span data-stu-id="aa380-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="aa380-130">專案名稱</span><span class="sxs-lookup"><span data-stu-id="aa380-130">Project name</span></span>
  * <span data-ttu-id="aa380-131">區</span><span class="sxs-lookup"><span data-stu-id="aa380-131">Areas</span></span>
    * <span data-ttu-id="aa380-132">產品</span><span class="sxs-lookup"><span data-stu-id="aa380-132">Products</span></span>
      * <span data-ttu-id="aa380-133">控制器</span><span class="sxs-lookup"><span data-stu-id="aa380-133">Controllers</span></span>
        * <span data-ttu-id="aa380-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="aa380-134">HomeController.cs</span></span>
        * <span data-ttu-id="aa380-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="aa380-135">ManageController.cs</span></span>
      * <span data-ttu-id="aa380-136">檢視</span><span class="sxs-lookup"><span data-stu-id="aa380-136">Views</span></span>
        * <span data-ttu-id="aa380-137">首頁</span><span class="sxs-lookup"><span data-stu-id="aa380-137">Home</span></span>
          * <span data-ttu-id="aa380-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-138">Index.cshtml</span></span>
        * <span data-ttu-id="aa380-139">管理</span><span class="sxs-lookup"><span data-stu-id="aa380-139">Manage</span></span>
          * <span data-ttu-id="aa380-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-140">Index.cshtml</span></span>
          * <span data-ttu-id="aa380-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-141">About.cshtml</span></span>
    * <span data-ttu-id="aa380-142">服務</span><span class="sxs-lookup"><span data-stu-id="aa380-142">Services</span></span>
      * <span data-ttu-id="aa380-143">控制器</span><span class="sxs-lookup"><span data-stu-id="aa380-143">Controllers</span></span>
        * <span data-ttu-id="aa380-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="aa380-144">HomeController.cs</span></span>
      * <span data-ttu-id="aa380-145">檢視</span><span class="sxs-lookup"><span data-stu-id="aa380-145">Views</span></span>
        * <span data-ttu-id="aa380-146">首頁</span><span class="sxs-lookup"><span data-stu-id="aa380-146">Home</span></span>
          * <span data-ttu-id="aa380-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-147">Index.cshtml</span></span>

<span data-ttu-id="aa380-148">儘管上述配置通常會在使用區域時使用，但是只需有檢視檔案，就能使用此資料夾結構。</span><span class="sxs-lookup"><span data-stu-id="aa380-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="aa380-149">檢視探索會依下列順序搜尋相符的區域檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="aa380-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="aa380-150">使控制器與區域產生關聯</span><span class="sxs-lookup"><span data-stu-id="aa380-150">Associate the controller with an Area</span></span>

<span data-ttu-id="aa380-151">區網域控制站會使用[ &lbrack; 區域 &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)屬性來指定：</span><span class="sxs-lookup"><span data-stu-id="aa380-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="aa380-152">新增區域路由</span><span class="sxs-lookup"><span data-stu-id="aa380-152">Add Area route</span></span>

<span data-ttu-id="aa380-153">區域路由通常會使用一般  [路由](xref:mvc/controllers/routing#cr) ，而不是 [屬性路由](xref:mvc/controllers/routing#ar)。</span><span class="sxs-lookup"><span data-stu-id="aa380-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="aa380-154">慣例路由與順序息息相關。</span><span class="sxs-lookup"><span data-stu-id="aa380-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="aa380-155">一般而言，具有區域的路由應該放在路由表前面，因為這些路由比沒有區域的路由更明確。</span><span class="sxs-lookup"><span data-stu-id="aa380-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="aa380-156">如果 URL 空間在所有區域中都是統一的，則可使用 `{area:...}` 作為路由範本中的語彙基元：</span><span class="sxs-lookup"><span data-stu-id="aa380-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="aa380-157">在上述程式碼中，`exists` 會套用路由必須與區域相符的條件約束。</span><span class="sxs-lookup"><span data-stu-id="aa380-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="aa380-158">使用 `{area:...}` with `MapControllerRoute` ：</span><span class="sxs-lookup"><span data-stu-id="aa380-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="aa380-159">是將路由新增至區域最不復雜的機制。</span><span class="sxs-lookup"><span data-stu-id="aa380-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="aa380-160">符合具有屬性的所有控制器 `[Area("Area name")]` 。</span><span class="sxs-lookup"><span data-stu-id="aa380-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="aa380-161">下列程式碼會使用 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 來建立兩個具名的區域路由：</span><span class="sxs-lookup"><span data-stu-id="aa380-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="aa380-162">如需詳細資訊，請參閱[區域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="aa380-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="aa380-163">使用 MVC 區域產生連結</span><span class="sxs-lookup"><span data-stu-id="aa380-163">Link generation with MVC areas</span></span>

<span data-ttu-id="aa380-164">以下來自[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples)的程式碼會顯示使用指定的區域來產生連結：</span><span class="sxs-lookup"><span data-stu-id="aa380-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="aa380-165">範例下載包含 [部分視圖](xref:mvc/views/partial) ，其中包含：</span><span class="sxs-lookup"><span data-stu-id="aa380-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="aa380-166">上述連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-166">The preceding links.</span></span>
* <span data-ttu-id="aa380-167">未指定類似上述的連結 `area` 。</span><span class="sxs-lookup"><span data-stu-id="aa380-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="aa380-168">部分檢視會在[配置檔案](xref:mvc/views/layout)中進行參考，因此，應用程式中的每個頁面都會顯示產生的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="aa380-169">在未指定區域的情況下產生的連結，只有在從相同區域與控制器中的頁面進行參考時才有效。</span><span class="sxs-lookup"><span data-stu-id="aa380-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="aa380-170">未指定區域或控制站時，路由即會取決於「環境」[](xref:mvc/controllers/routing#ambient)值。</span><span class="sxs-lookup"><span data-stu-id="aa380-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="aa380-171">目前要求的目前路由值被視為用於連結產生的環境值。</span><span class="sxs-lookup"><span data-stu-id="aa380-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="aa380-172">在範例應用程式的許多情況下，使用環境值會產生未指定區域的標記不正確的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="aa380-173">如需詳細資訊，請參閱[路由至控制器動作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="aa380-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="aa380-174">使用 _ViewStart.cshtml 檔案共用區域的配置</span><span class="sxs-lookup"><span data-stu-id="aa380-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="aa380-175">若要共用整個應用程式的一般版面配置，請在 [應用程式根資料夾](#arf)中保留 *_ViewStart 的 cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="aa380-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="aa380-176">如需詳細資訊，請參閱<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="aa380-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="aa380-177">應用程式根資料夾</span><span class="sxs-lookup"><span data-stu-id="aa380-177">Application root folder</span></span>

<span data-ttu-id="aa380-178">應用程式根資料夾是在使用 ASP.NET Core 範本建立的 web 應用程式中包含 *Startup.cs* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa380-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="aa380-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="aa380-180">*/Views/_ViewImports cshtml*、適用于 MVC 的 */Pages/_ViewImports* 和頁面的 Razor ，不會匯入區域中的 Views。</span><span class="sxs-lookup"><span data-stu-id="aa380-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="aa380-181">您可以使用下列其中一種方法，提供所有視圖的 view imports：</span><span class="sxs-lookup"><span data-stu-id="aa380-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="aa380-182">將 *_ViewImports 的 cshtml* 加入至 [應用程式根資料夾](#arf)。</span><span class="sxs-lookup"><span data-stu-id="aa380-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="aa380-183">應用程式根資料夾中的 *_ViewImports* 會套用至應用程式中的所有 views。</span><span class="sxs-lookup"><span data-stu-id="aa380-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="aa380-184">將 *_ViewImports 的 cshtml* 檔案複製到 [區域] 底下的適當 [view] 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aa380-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="aa380-185">*_ViewImports 的 cshtml* 檔案 [通常包含卷](xref:mvc/views/tag-helpers/intro)標協助程式 imports、 `@using` 和 `@inject` 語句。</span><span class="sxs-lookup"><span data-stu-id="aa380-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="aa380-186">如需詳細資訊，請參閱匯 [入共用](xref:mvc/views/layout#importing-shared-directives)指示詞。</span><span class="sxs-lookup"><span data-stu-id="aa380-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="aa380-187">變更檢視儲存所在的預設區域資料夾</span><span class="sxs-lookup"><span data-stu-id="aa380-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="aa380-188">下列程式碼會將預設的區域資料夾從 `"Areas"` 變更為 `"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="aa380-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="aa380-189">具有頁面的區域 Razor</span><span class="sxs-lookup"><span data-stu-id="aa380-189">Areas with Razor Pages</span></span>

<span data-ttu-id="aa380-190">具有頁面的區域 Razor 需要 `Areas/<area name>/Pages` 應用程式根目錄中的資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa380-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="aa380-191">下列資料夾結構搭配[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples)一起使用：</span><span class="sxs-lookup"><span data-stu-id="aa380-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="aa380-192">專案名稱</span><span class="sxs-lookup"><span data-stu-id="aa380-192">Project name</span></span>
  * <span data-ttu-id="aa380-193">區</span><span class="sxs-lookup"><span data-stu-id="aa380-193">Areas</span></span>
    * <span data-ttu-id="aa380-194">產品</span><span class="sxs-lookup"><span data-stu-id="aa380-194">Products</span></span>
      * <span data-ttu-id="aa380-195">頁面</span><span class="sxs-lookup"><span data-stu-id="aa380-195">Pages</span></span>
        * <span data-ttu-id="aa380-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="aa380-196">_ViewImports</span></span>
        * <span data-ttu-id="aa380-197">關於</span><span class="sxs-lookup"><span data-stu-id="aa380-197">About</span></span>
        * <span data-ttu-id="aa380-198">索引</span><span class="sxs-lookup"><span data-stu-id="aa380-198">Index</span></span>
    * <span data-ttu-id="aa380-199">服務</span><span class="sxs-lookup"><span data-stu-id="aa380-199">Services</span></span>
      * <span data-ttu-id="aa380-200">頁面</span><span class="sxs-lookup"><span data-stu-id="aa380-200">Pages</span></span>
        * <span data-ttu-id="aa380-201">管理</span><span class="sxs-lookup"><span data-stu-id="aa380-201">Manage</span></span>
          * <span data-ttu-id="aa380-202">關於</span><span class="sxs-lookup"><span data-stu-id="aa380-202">About</span></span>
          * <span data-ttu-id="aa380-203">索引</span><span class="sxs-lookup"><span data-stu-id="aa380-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="aa380-204">使用 Razor 頁面和區域產生連結</span><span class="sxs-lookup"><span data-stu-id="aa380-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="aa380-205">以下來自[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples/RPareas)的程式碼會顯示使用指定的區域 (例如 `asp-area="Products"`) 來產生連結：</span><span class="sxs-lookup"><span data-stu-id="aa380-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="aa380-206">範例下載包括[部分檢視](xref:mvc/views/partial)，其中可在未指定區域的情況下包含上述連結和相同連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="aa380-207">部分檢視會在[配置檔案](xref:mvc/views/layout)中進行參考，因此，應用程式中的每個頁面都會顯示產生的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="aa380-208">在未指定區域的情況下產生的連結，只有在從相同區域中的頁面進行參考時才有效。</span><span class="sxs-lookup"><span data-stu-id="aa380-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="aa380-209">未指定區域時，路由即會取決於「環境」值。</span><span class="sxs-lookup"><span data-stu-id="aa380-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="aa380-210">目前要求的目前路由值被視為用於連結產生的環境值。</span><span class="sxs-lookup"><span data-stu-id="aa380-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="aa380-211">在許多適用於範例應用程式的案例中，使用環境值會產生不正確的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="aa380-212">例如，試想從下列程式碼產生的連結：</span><span class="sxs-lookup"><span data-stu-id="aa380-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="aa380-213">針對上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="aa380-213">For the preceding code:</span></span>

* <span data-ttu-id="aa380-214">僅當最後一個要求是針對 `Services` 區域中的頁面時，從 `<a asp-page="/Manage/About">` 產生的連結才是正確的。</span><span class="sxs-lookup"><span data-stu-id="aa380-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="aa380-215">例如，`/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="aa380-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="aa380-216">僅當最後一個要求是針對 `/Home` 中的頁面時，從 `<a asp-page="/About">` 產生的連結才是正確的。</span><span class="sxs-lookup"><span data-stu-id="aa380-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="aa380-217">程式碼來自[下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="aa380-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="aa380-218">使用 _ViewImports 檔案匯入命名空間和標記協助程式</span><span class="sxs-lookup"><span data-stu-id="aa380-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="aa380-219">您可以將 *_ViewImports cshtml* 檔案新增至每個 [區域 *頁面* ] 資料夾，以將命名空間和標記協助程式匯入到資料夾中的每個 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="aa380-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="aa380-220">請考慮範例程式碼的 *Services* 區域，該區域不包含 *_ViewImports.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="aa380-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="aa380-221">下列標記顯示 */Services/Manage/About* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="aa380-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="aa380-222">在上述標記中：</span><span class="sxs-lookup"><span data-stu-id="aa380-222">In the preceding markup:</span></span>

* <span data-ttu-id="aa380-223">您必須使用完整的類別名稱來指定模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) 。</span><span class="sxs-lookup"><span data-stu-id="aa380-223">The fully qualified class name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="aa380-224">[標記協助程式](xref:mvc/views/tag-helpers/intro)由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 啟用</span><span class="sxs-lookup"><span data-stu-id="aa380-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="aa380-225">在範例下載中，Products 區域包含下列 *_ViewImports.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="aa380-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="aa380-226">下列標記顯示 */Products/About* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="aa380-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="aa380-227">在上述檔案中，命名空間和 `@addTagHelper` 指示詞會由 *Areas/Products/Pages/_ViewImports.cshtml* 檔案匯入到檔案。</span><span class="sxs-lookup"><span data-stu-id="aa380-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="aa380-228">如需詳細資訊，請參閱[管理標籤協助程式範圍](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[匯入共用指示詞](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="aa380-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="aa380-229">頁面區域的共用版面配置 Razor</span><span class="sxs-lookup"><span data-stu-id="aa380-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="aa380-230">若要針對整個應用程式共用通用的配置，請將 *_ViewStart.cshtml* 移至應用程式根資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa380-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="aa380-231">發行區域</span><span class="sxs-lookup"><span data-stu-id="aa380-231">Publishing Areas</span></span>

<span data-ttu-id="aa380-232">當 \*.csproj 檔案中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 時，會將所有 \*.cshtml 檔案及 *wwwroot* 目錄內的檔案發佈至輸出。</span><span class="sxs-lookup"><span data-stu-id="aa380-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aa380-233">區域是 ASP.NET 功能，可用來將相關功能組織為群組，以作為個別的命名空間 (適用於路由) 和資料夾結構 (適用於檢視)。</span><span class="sxs-lookup"><span data-stu-id="aa380-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="aa380-234">使用區域會藉由將另一個路由參數、 `area` 、新增至 `controller` 和 `action` 或 Razor 頁面，來建立路由用途的階層 `page` 。</span><span class="sxs-lookup"><span data-stu-id="aa380-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="aa380-235">區域可讓您將 ASP.NET Core Web 應用程式分割成較小的功能群組，每個群組都有自己的一組 Razor 頁面、控制器、視圖和模型。</span><span class="sxs-lookup"><span data-stu-id="aa380-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="aa380-236">一個區域基本上是應用程式內的一個結構。</span><span class="sxs-lookup"><span data-stu-id="aa380-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="aa380-237">在 ASP.NET Core Web 專案中，Pages、模型、控制器和檢視等邏輯元件都會保留在不同的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aa380-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="aa380-238">ASP.NET Core 執行階段會使用命名慣例來建立這些元件之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="aa380-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="aa380-239">針對大型應用程式，將應用程式分割成個別高功能層級區域可能較有利。</span><span class="sxs-lookup"><span data-stu-id="aa380-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="aa380-240">舉例來說，一個電子商務應用程式可具有多個業務單位，例如結帳、計費和搜尋。</span><span class="sxs-lookup"><span data-stu-id="aa380-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="aa380-241">每個單位都有自己的區域，以包含視圖、控制器、 Razor 頁面和模型。</span><span class="sxs-lookup"><span data-stu-id="aa380-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="aa380-242">處於下列情況時，請考慮在專案中使用區域：</span><span class="sxs-lookup"><span data-stu-id="aa380-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="aa380-243">應用程式是由可以邏輯方式區隔的多個高階功能性元件所組成的。</span><span class="sxs-lookup"><span data-stu-id="aa380-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="aa380-244">您想要分割應用程式，讓每個功能區域都可獨立運作。</span><span class="sxs-lookup"><span data-stu-id="aa380-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="aa380-245">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aa380-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="aa380-246">下載範例提供基本的應用程式來測試區域。</span><span class="sxs-lookup"><span data-stu-id="aa380-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="aa380-247">如果您使用的是 Razor 頁面，請參閱本檔中 [含有 Razor 頁面的區域](#areas-with-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="aa380-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="aa380-248">適用於控制器與檢視的區域</span><span class="sxs-lookup"><span data-stu-id="aa380-248">Areas for controllers with views</span></span>

<span data-ttu-id="aa380-249">使用區域、控制器及檢視的典型 ASP.NET Core Web 應用程式包含下列項目：</span><span class="sxs-lookup"><span data-stu-id="aa380-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="aa380-250">一個[區域資料夾結構](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="aa380-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="aa380-251">具有屬性的控制器， [`[Area]`](#attribute) 可讓控制器與區域產生關聯：</span><span class="sxs-lookup"><span data-stu-id="aa380-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="aa380-252">[新增至啟動的區域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="aa380-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="aa380-253">區域資料夾結構</span><span class="sxs-lookup"><span data-stu-id="aa380-253">Area folder structure</span></span>

<span data-ttu-id="aa380-254">假設應用程式具有兩個邏輯群組：「產品」和「服務」。</span><span class="sxs-lookup"><span data-stu-id="aa380-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="aa380-255">使用區域，資料夾結構應該如下：</span><span class="sxs-lookup"><span data-stu-id="aa380-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="aa380-256">專案名稱</span><span class="sxs-lookup"><span data-stu-id="aa380-256">Project name</span></span>
  * <span data-ttu-id="aa380-257">區</span><span class="sxs-lookup"><span data-stu-id="aa380-257">Areas</span></span>
    * <span data-ttu-id="aa380-258">產品</span><span class="sxs-lookup"><span data-stu-id="aa380-258">Products</span></span>
      * <span data-ttu-id="aa380-259">控制器</span><span class="sxs-lookup"><span data-stu-id="aa380-259">Controllers</span></span>
        * <span data-ttu-id="aa380-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="aa380-260">HomeController.cs</span></span>
        * <span data-ttu-id="aa380-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="aa380-261">ManageController.cs</span></span>
      * <span data-ttu-id="aa380-262">檢視</span><span class="sxs-lookup"><span data-stu-id="aa380-262">Views</span></span>
        * <span data-ttu-id="aa380-263">首頁</span><span class="sxs-lookup"><span data-stu-id="aa380-263">Home</span></span>
          * <span data-ttu-id="aa380-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-264">Index.cshtml</span></span>
        * <span data-ttu-id="aa380-265">管理</span><span class="sxs-lookup"><span data-stu-id="aa380-265">Manage</span></span>
          * <span data-ttu-id="aa380-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-266">Index.cshtml</span></span>
          * <span data-ttu-id="aa380-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-267">About.cshtml</span></span>
    * <span data-ttu-id="aa380-268">服務</span><span class="sxs-lookup"><span data-stu-id="aa380-268">Services</span></span>
      * <span data-ttu-id="aa380-269">控制器</span><span class="sxs-lookup"><span data-stu-id="aa380-269">Controllers</span></span>
        * <span data-ttu-id="aa380-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="aa380-270">HomeController.cs</span></span>
      * <span data-ttu-id="aa380-271">檢視</span><span class="sxs-lookup"><span data-stu-id="aa380-271">Views</span></span>
        * <span data-ttu-id="aa380-272">首頁</span><span class="sxs-lookup"><span data-stu-id="aa380-272">Home</span></span>
          * <span data-ttu-id="aa380-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-273">Index.cshtml</span></span>

<span data-ttu-id="aa380-274">儘管上述配置通常會在使用區域時使用，但是只需有檢視檔案，就能使用此資料夾結構。</span><span class="sxs-lookup"><span data-stu-id="aa380-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="aa380-275">檢視探索會依下列順序搜尋相符的區域檢視檔案：</span><span class="sxs-lookup"><span data-stu-id="aa380-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="aa380-276">使控制器與區域產生關聯</span><span class="sxs-lookup"><span data-stu-id="aa380-276">Associate the controller with an Area</span></span>

<span data-ttu-id="aa380-277">區網域控制站會使用[ &lbrack; 區域 &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)屬性來指定：</span><span class="sxs-lookup"><span data-stu-id="aa380-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="aa380-278">新增區域路由</span><span class="sxs-lookup"><span data-stu-id="aa380-278">Add Area route</span></span>

<span data-ttu-id="aa380-279">區域路由通常會使用慣例路由，而非屬性路由。</span><span class="sxs-lookup"><span data-stu-id="aa380-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="aa380-280">慣例路由與順序息息相關。</span><span class="sxs-lookup"><span data-stu-id="aa380-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="aa380-281">一般而言，具有區域的路由應該放在路由表前面，因為這些路由比沒有區域的路由更明確。</span><span class="sxs-lookup"><span data-stu-id="aa380-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="aa380-282">如果 URL 空間在所有區域中都是統一的，則可使用 `{area:...}` 作為路由範本中的語彙基元：</span><span class="sxs-lookup"><span data-stu-id="aa380-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="aa380-283">在上述程式碼中，`exists` 會套用路由必須與區域相符的條件約束。</span><span class="sxs-lookup"><span data-stu-id="aa380-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="aa380-284">若要將路由新增至區域，使用 `{area:...}` 是最簡單的機制。</span><span class="sxs-lookup"><span data-stu-id="aa380-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="aa380-285">下列程式碼會使用 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 來建立兩個具名的區域路由：</span><span class="sxs-lookup"><span data-stu-id="aa380-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="aa380-286">搭配 ASP.NET Core 2.2 使用 `MapAreaRoute` 時，請參閱[這個 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/7772) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="aa380-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="aa380-287">如需詳細資訊，請參閱[區域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="aa380-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="aa380-288">使用 MVC 區域產生連結</span><span class="sxs-lookup"><span data-stu-id="aa380-288">Link generation with MVC areas</span></span>

<span data-ttu-id="aa380-289">以下來自[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples)的程式碼會顯示使用指定的區域來產生連結：</span><span class="sxs-lookup"><span data-stu-id="aa380-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="aa380-290">使用上述程式碼產生的連結，在應用程式中的任何位置都是有效的。</span><span class="sxs-lookup"><span data-stu-id="aa380-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="aa380-291">範例下載包括[部分檢視](xref:mvc/views/partial)，其中可在未指定區域的情況下包含上述連結和相同連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="aa380-292">部分檢視會在[配置檔案](xref:mvc/views/layout)中進行參考，因此，應用程式中的每個頁面都會顯示產生的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="aa380-293">在未指定區域的情況下產生的連結，只有在從相同區域與控制器中的頁面進行參考時才有效。</span><span class="sxs-lookup"><span data-stu-id="aa380-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="aa380-294">未指定區域或控制站時，路由即會取決於「環境」值。</span><span class="sxs-lookup"><span data-stu-id="aa380-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="aa380-295">目前要求的目前路由值被視為用於連結產生的環境值。</span><span class="sxs-lookup"><span data-stu-id="aa380-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="aa380-296">在許多適用於範例應用程式的案例中，使用環境值會產生不正確的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="aa380-297">如需詳細資訊，請參閱[路由至控制器動作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="aa380-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="aa380-298">使用 _ViewStart.cshtml 檔案共用區域的配置</span><span class="sxs-lookup"><span data-stu-id="aa380-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="aa380-299">若要針對整個應用程式共用通用的配置，請將 *_ViewStart.cshtml* 移至應用程式根資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa380-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="aa380-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="aa380-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="aa380-301">在其標準位置中，*/Views/_ViewImports.cshtml* 不適用於區域。</span><span class="sxs-lookup"><span data-stu-id="aa380-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="aa380-302">若要在您的區域中 [使用一般卷](xref:mvc/views/tag-helpers/intro)標協助程式、或，請 `@using` `@inject` 確定適當的 *_ViewImports cshtml* 檔案 [適用于您的區域查看](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="aa380-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="aa380-303">如果您希望在所有檢視中都有相同的行為，請將 */Views/_ViewImports.cshtml* 移至應用程式根目錄。</span><span class="sxs-lookup"><span data-stu-id="aa380-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="aa380-304">變更檢視儲存所在的預設區域資料夾</span><span class="sxs-lookup"><span data-stu-id="aa380-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="aa380-305">下列程式碼會將預設的區域資料夾從 `"Areas"` 變更為 `"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="aa380-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="aa380-306">具有頁面的區域 Razor</span><span class="sxs-lookup"><span data-stu-id="aa380-306">Areas with Razor Pages</span></span>

<span data-ttu-id="aa380-307">具有頁面的區域 Razor 需要 `Areas/<area name>/Pages` 應用程式根目錄中的資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa380-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="aa380-308">下列資料夾結構搭配[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples)一起使用：</span><span class="sxs-lookup"><span data-stu-id="aa380-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="aa380-309">專案名稱</span><span class="sxs-lookup"><span data-stu-id="aa380-309">Project name</span></span>
  * <span data-ttu-id="aa380-310">區</span><span class="sxs-lookup"><span data-stu-id="aa380-310">Areas</span></span>
    * <span data-ttu-id="aa380-311">產品</span><span class="sxs-lookup"><span data-stu-id="aa380-311">Products</span></span>
      * <span data-ttu-id="aa380-312">頁面</span><span class="sxs-lookup"><span data-stu-id="aa380-312">Pages</span></span>
        * <span data-ttu-id="aa380-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="aa380-313">_ViewImports</span></span>
        * <span data-ttu-id="aa380-314">關於</span><span class="sxs-lookup"><span data-stu-id="aa380-314">About</span></span>
        * <span data-ttu-id="aa380-315">索引</span><span class="sxs-lookup"><span data-stu-id="aa380-315">Index</span></span>
    * <span data-ttu-id="aa380-316">服務</span><span class="sxs-lookup"><span data-stu-id="aa380-316">Services</span></span>
      * <span data-ttu-id="aa380-317">頁面</span><span class="sxs-lookup"><span data-stu-id="aa380-317">Pages</span></span>
        * <span data-ttu-id="aa380-318">管理</span><span class="sxs-lookup"><span data-stu-id="aa380-318">Manage</span></span>
          * <span data-ttu-id="aa380-319">關於</span><span class="sxs-lookup"><span data-stu-id="aa380-319">About</span></span>
          * <span data-ttu-id="aa380-320">索引</span><span class="sxs-lookup"><span data-stu-id="aa380-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="aa380-321">使用 Razor 頁面和區域產生連結</span><span class="sxs-lookup"><span data-stu-id="aa380-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="aa380-322">以下來自[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples/RPareas)的程式碼會顯示使用指定的區域 (例如 `asp-area="Products"`) 來產生連結：</span><span class="sxs-lookup"><span data-stu-id="aa380-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="aa380-323">使用上述程式碼產生的連結，在應用程式中的任何位置都是有效的。</span><span class="sxs-lookup"><span data-stu-id="aa380-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="aa380-324">範例下載包括[部分檢視](xref:mvc/views/partial)，其中可在未指定區域的情況下包含上述連結和相同連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="aa380-325">部分檢視會在[配置檔案](xref:mvc/views/layout)中進行參考，因此，應用程式中的每個頁面都會顯示產生的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="aa380-326">在未指定區域的情況下產生的連結，只有在從相同區域中的頁面進行參考時才有效。</span><span class="sxs-lookup"><span data-stu-id="aa380-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="aa380-327">未指定區域時，路由即會取決於「環境」值。</span><span class="sxs-lookup"><span data-stu-id="aa380-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="aa380-328">目前要求的目前路由值被視為用於連結產生的環境值。</span><span class="sxs-lookup"><span data-stu-id="aa380-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="aa380-329">在許多適用於範例應用程式的案例中，使用環境值會產生不正確的連結。</span><span class="sxs-lookup"><span data-stu-id="aa380-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="aa380-330">例如，試想從下列程式碼產生的連結：</span><span class="sxs-lookup"><span data-stu-id="aa380-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="aa380-331">針對上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="aa380-331">For the preceding code:</span></span>

* <span data-ttu-id="aa380-332">僅當最後一個要求是針對 `Services` 區域中的頁面時，從 `<a asp-page="/Manage/About">` 產生的連結才是正確的。</span><span class="sxs-lookup"><span data-stu-id="aa380-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="aa380-333">例如，`/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="aa380-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="aa380-334">僅當最後一個要求是針對 `/Home` 中的頁面時，從 `<a asp-page="/About">` 產生的連結才是正確的。</span><span class="sxs-lookup"><span data-stu-id="aa380-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="aa380-335">程式碼來自[下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="aa380-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="aa380-336">使用 _ViewImports 檔案匯入命名空間和標記協助程式</span><span class="sxs-lookup"><span data-stu-id="aa380-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="aa380-337">您可以將 *_ViewImports cshtml* 檔案新增至每個 [區域 *頁面* ] 資料夾，以將命名空間和標記協助程式匯入到資料夾中的每個 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="aa380-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="aa380-338">請考慮範例程式碼的 *Services* 區域，該區域不包含 *_ViewImports.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="aa380-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="aa380-339">下列標記顯示 */Services/Manage/About* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="aa380-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="aa380-340">在上述標記中：</span><span class="sxs-lookup"><span data-stu-id="aa380-340">In the preceding markup:</span></span>

* <span data-ttu-id="aa380-341">必須使用完整的網域名稱來指定此模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="aa380-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="aa380-342">[標記協助程式](xref:mvc/views/tag-helpers/intro)由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 啟用</span><span class="sxs-lookup"><span data-stu-id="aa380-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="aa380-343">在範例下載中，Products 區域包含下列 *_ViewImports.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="aa380-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="aa380-344">下列標記顯示 */Products/About* Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="aa380-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="aa380-345">在上述檔案中，命名空間和 `@addTagHelper` 指示詞會由 *Areas/Products/Pages/_ViewImports.cshtml* 檔案匯入到檔案。</span><span class="sxs-lookup"><span data-stu-id="aa380-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="aa380-346">如需詳細資訊，請參閱[管理標籤協助程式範圍](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[匯入共用指示詞](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="aa380-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="aa380-347">頁面區域的共用版面配置 Razor</span><span class="sxs-lookup"><span data-stu-id="aa380-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="aa380-348">若要針對整個應用程式共用通用的配置，請將 *_ViewStart.cshtml* 移至應用程式根資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa380-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="aa380-349">發行區域</span><span class="sxs-lookup"><span data-stu-id="aa380-349">Publishing Areas</span></span>

<span data-ttu-id="aa380-350">當 \*.csproj 檔案中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 時，會將所有 \*.cshtml 檔案及 *wwwroot* 目錄內的檔案發佈至輸出。</span><span class="sxs-lookup"><span data-stu-id="aa380-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
