---
title: ASP.NET Core Blazor 版面配置
author: guardrex
description: 瞭解如何為應用程式建立可重複使用的版面配置元件 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: d1f3e2028ca120b5901aca0b24802d872ae52597
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279730"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="f77b3-103">ASP.NET Core Blazor 版面配置</span><span class="sxs-lookup"><span data-stu-id="f77b3-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="f77b3-104">某些應用程式專案（例如功能表、著作權訊息和公司標誌）通常是應用程式整體版面配置的一部分，且應用程式中的每個元件都會使用這些元素。</span><span class="sxs-lookup"><span data-stu-id="f77b3-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="f77b3-105">將這些專案的程式碼複製到應用程式的所有元件，並不是有效的方法。</span><span class="sxs-lookup"><span data-stu-id="f77b3-105">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="f77b3-106">每次其中一個元素需要更新時，都必須更新每個元件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-106">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="f77b3-107">這類複製很難維護，而且可能會在一段時間後產生不一致的內容。</span><span class="sxs-lookup"><span data-stu-id="f77b3-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="f77b3-108">*版面* 配置會解決此問題。</span><span class="sxs-lookup"><span data-stu-id="f77b3-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="f77b3-109">技術上來說，版面配置只是另一個元件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="f77b3-110">版面配置是在 Razor 範本或 c # 程式碼中定義，而且可以使用 [資料](xref:blazor/components/data-binding)系結、相依性 [插入](xref:blazor/fundamentals/dependency-injection)和其他元件案例。</span><span class="sxs-lookup"><span data-stu-id="f77b3-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span> <span data-ttu-id="f77b3-111">版面配置僅適用于 Razor 具有指示詞的可路由元件 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-111">Layouts only apply to routable Razor components that have [`@page`](xref:mvc/views/razor#page) directives.</span></span>

<span data-ttu-id="f77b3-112">若要將元件轉換成版面配置：</span><span class="sxs-lookup"><span data-stu-id="f77b3-112">To convert a component into a layout:</span></span>

* <span data-ttu-id="f77b3-113">從繼承元件 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-113">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="f77b3-114">會 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 定義配置 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 內所呈現內容的屬性。</span><span class="sxs-lookup"><span data-stu-id="f77b3-114">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="f77b3-115">使用 Razor 語法 `@Body` 來指定版面配置標記中轉譯內容的位置。</span><span class="sxs-lookup"><span data-stu-id="f77b3-115">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="f77b3-116">下列程式碼範例會顯示 Razor 版面配置元件的範本 `MainLayout.razor` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-116">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="f77b3-117">版面配置會繼承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 並設定 `@Body` 導覽列和頁尾之間的：</span><span class="sxs-lookup"><span data-stu-id="f77b3-117">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="f77b3-118">`MainLayout` 元件</span><span class="sxs-lookup"><span data-stu-id="f77b3-118">`MainLayout` component</span></span>

<span data-ttu-id="f77b3-119">在以其中一個專案範本為基礎的應用程式中 Blazor ， `MainLayout` (`MainLayout.razor`) 元件位於應用程式的 `Shared` 資料夾中：</span><span class="sxs-lookup"><span data-stu-id="f77b3-119">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="f77b3-120">預設配置</span><span class="sxs-lookup"><span data-stu-id="f77b3-120">Default layout</span></span>

<span data-ttu-id="f77b3-121">在應用程式檔案的元件中指定預設的應用程式版面配置 <xref:Microsoft.AspNetCore.Components.Routing.Router> `App.razor` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-121">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="f77b3-122">下列 <xref:Microsoft.AspNetCore.Components.Routing.Router> 元件是由預設範本所提供，會 Blazor 將預設版面配置設定為 `MainLayout` 元件：</span><span class="sxs-lookup"><span data-stu-id="f77b3-122">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="f77b3-123">若要為內容提供預設版面配置 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> ，請 <xref:Microsoft.AspNetCore.Components.LayoutView> 指定 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容的：</span><span class="sxs-lookup"><span data-stu-id="f77b3-123">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="f77b3-124">如需元件的詳細資訊 <xref:Microsoft.AspNetCore.Components.Routing.Router> ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-124">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="f77b3-125">將配置指定為路由器中的預設配置，是很有用的作法，因為它可以覆寫每個元件或每個資料夾。</span><span class="sxs-lookup"><span data-stu-id="f77b3-125">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="f77b3-126">偏好使用路由器來設定應用程式的預設版面配置，因為這是最常見的技巧。</span><span class="sxs-lookup"><span data-stu-id="f77b3-126">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="f77b3-127">指定元件中的版面配置</span><span class="sxs-lookup"><span data-stu-id="f77b3-127">Specify a layout in a component</span></span>

<span data-ttu-id="f77b3-128">使用指示詞將配置套用 [`@layout`](xref:mvc/views/razor#layout) Razor 至也有指示詞的可路由 Razor 元件 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-128">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that also has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="f77b3-129">編譯器會將套用 `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> 至元件類別的轉換成。</span><span class="sxs-lookup"><span data-stu-id="f77b3-129">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="f77b3-130">下列元件的內容 `MasterList` 會插入到的 `MasterLayout` 位置 `@Body` ：</span><span class="sxs-lookup"><span data-stu-id="f77b3-130">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="f77b3-131">直接在元件中指定版面配置，會覆寫路由器中的 *預設* 組態集，或從匯入的指示詞 `@layout` `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-131">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="f77b3-132">集中式版面配置選取</span><span class="sxs-lookup"><span data-stu-id="f77b3-132">Centralized layout selection</span></span>

<span data-ttu-id="f77b3-133">應用程式的每個資料夾都可以選擇性地包含名為的範本檔案 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-133">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="f77b3-134">編譯器包含在相同資料夾中所有範本的 imports 檔中所指定的指示詞 Razor ，並以遞迴方式在其所有子資料夾中包含這些指令程式。</span><span class="sxs-lookup"><span data-stu-id="f77b3-134">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="f77b3-135">因此， `_Imports.razor` 包含的檔案 `@layout MyCoolLayout` 可確保資料夾中的所有元件都使用 `MyCoolLayout` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-135">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="f77b3-136">不需要重複新增 `@layout MyCoolLayout` 至 `.razor` 資料夾和子資料夾內的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="f77b3-136">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="f77b3-137">`@using` 指示詞也會以同樣的方式套用至元件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-137">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="f77b3-138">下列檔案會匯 `_Imports.razor` 入：</span><span class="sxs-lookup"><span data-stu-id="f77b3-138">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="f77b3-139">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="f77b3-139">`MyCoolLayout`.</span></span>
* <span data-ttu-id="f77b3-140">Razor相同資料夾和任何子資料夾中的所有元件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-140">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="f77b3-141">`BlazorApp1.Data` 命名空間。</span><span class="sxs-lookup"><span data-stu-id="f77b3-141">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="f77b3-142">檔案 `_Imports.razor` 類似于 [ Razor views 和 pages 的 _ViewImports cshtml](xref:mvc/views/layout#importing-shared-directives) 檔案，但特別適用于 Razor 元件檔。</span><span class="sxs-lookup"><span data-stu-id="f77b3-142">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="f77b3-143">在中指定配置會 `_Imports.razor` 覆寫指定為路由器 *預設版面* 配置的版面配置。</span><span class="sxs-lookup"><span data-stu-id="f77b3-143">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="f77b3-144">請勿將指示詞新增 Razor `@layout` 至根檔案 `_Imports.razor` ，這會導致應用程式中的版面配置無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="f77b3-144">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="f77b3-145">若要控制預設的應用程式版面配置，請在元件中指定版面配置 `Router` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-145">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="f77b3-146">如需詳細資訊，請參閱 [預設版面](#default-layout) 配置區段。</span><span class="sxs-lookup"><span data-stu-id="f77b3-146">For more information, see the [Default layout](#default-layout) section.</span></span>

> [!NOTE]
> <span data-ttu-id="f77b3-147">指示詞 [`@layout`](xref:mvc/views/razor#layout) Razor 只會將版面配置套用至具有指示詞的可路由 Razor 元件 [`@page`](xref:mvc/views/razor#page) 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-147">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with [`@page`](xref:mvc/views/razor#page) directives.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="f77b3-148">嵌套版面配置</span><span class="sxs-lookup"><span data-stu-id="f77b3-148">Nested layouts</span></span>

<span data-ttu-id="f77b3-149">應用程式可由嵌套配置組成。</span><span class="sxs-lookup"><span data-stu-id="f77b3-149">Apps can consist of nested layouts.</span></span> <span data-ttu-id="f77b3-150">元件可以參考配置，進而參考另一個版面配置。</span><span class="sxs-lookup"><span data-stu-id="f77b3-150">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="f77b3-151">例如，使用嵌套配置來建立多層式功能表結構。</span><span class="sxs-lookup"><span data-stu-id="f77b3-151">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="f77b3-152">下列範例示範如何使用嵌套版面配置。</span><span class="sxs-lookup"><span data-stu-id="f77b3-152">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="f77b3-153">檔案 `EpisodesComponent.razor` 是要顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-153">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="f77b3-154">元件會參考 `MasterListLayout` ：</span><span class="sxs-lookup"><span data-stu-id="f77b3-154">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="f77b3-155">檔案會 `MasterListLayout.razor` 提供 `MasterListLayout` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-155">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="f77b3-156">版面配置會參考另一個版面配置， `MasterLayout` 也就是轉譯的位置。</span><span class="sxs-lookup"><span data-stu-id="f77b3-156">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="f77b3-157">`EpisodesComponent` 會呈現出現的位置 `@Body` ：</span><span class="sxs-lookup"><span data-stu-id="f77b3-157">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="f77b3-158">最後， `MasterLayout` 在中 `MasterLayout.razor` 包含最上層的版面配置元素，例如頁首、主功能表和頁尾。</span><span class="sxs-lookup"><span data-stu-id="f77b3-158">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="f77b3-159">`MasterListLayout` 使用 `EpisodesComponent` 呈現時，會 `@Body` 出現：</span><span class="sxs-lookup"><span data-stu-id="f77b3-159">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="f77b3-160">Razor使用整合元件共用頁面配置</span><span class="sxs-lookup"><span data-stu-id="f77b3-160">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="f77b3-161">當可路由的元件整合到 Razor 頁面應用程式時，應用程式的共用配置可以與元件一起使用。</span><span class="sxs-lookup"><span data-stu-id="f77b3-161">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="f77b3-162">如需詳細資訊，請參閱<xref:blazor/components/prerendering-and-integration>。</span><span class="sxs-lookup"><span data-stu-id="f77b3-162">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f77b3-163">其他資源</span><span class="sxs-lookup"><span data-stu-id="f77b3-163">Additional resources</span></span>

* <xref:mvc/views/layout>
