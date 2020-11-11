---
title: 'ASP.NET Core :::no-loc(Blazor)::: 版面配置'
author: guardrex
description: '瞭解如何為應用程式建立可重複使用的版面配置元件 :::no-loc(Blazor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
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
uid: blazor/layouts
ms.openlocfilehash: 9462b73ad67394e79de08e7d2b13bf6a3145a04e
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507963"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="45354-103">ASP.NET Core :::no-loc(Blazor)::: 版面配置</span><span class="sxs-lookup"><span data-stu-id="45354-103">ASP.NET Core :::no-loc(Blazor)::: layouts</span></span>

<span data-ttu-id="45354-104">依 [Rainer Stropek](https://www.timecockpit.com) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="45354-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="45354-105">某些應用程式專案（例如功能表、著作權訊息和公司標誌）通常是應用程式整體版面配置的一部分，且應用程式中的每個元件都會使用這些元素。</span><span class="sxs-lookup"><span data-stu-id="45354-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="45354-106">將這些專案的程式碼複製到應用程式的所有元件，並不是有效的方法。</span><span class="sxs-lookup"><span data-stu-id="45354-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="45354-107">每次其中一個元素需要更新時，都必須更新每個元件。</span><span class="sxs-lookup"><span data-stu-id="45354-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="45354-108">這類複製很難維護，而且可能會在一段時間後產生不一致的內容。</span><span class="sxs-lookup"><span data-stu-id="45354-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="45354-109">*版面* 配置會解決此問題。</span><span class="sxs-lookup"><span data-stu-id="45354-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="45354-110">技術上來說，版面配置只是另一個元件。</span><span class="sxs-lookup"><span data-stu-id="45354-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="45354-111">版面配置是在 :::no-loc(Razor)::: 範本或 c # 程式碼中定義，而且可以使用 [資料](xref:blazor/components/data-binding)系結、相依性 [插入](xref:blazor/fundamentals/dependency-injection)和其他元件案例。</span><span class="sxs-lookup"><span data-stu-id="45354-111">A layout is defined in a :::no-loc(Razor)::: template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="45354-112">若要將 *元件* 變成配置 *，元件* ：</span><span class="sxs-lookup"><span data-stu-id="45354-112">To turn a *component* into a *layout* , the component:</span></span>

* <span data-ttu-id="45354-113">繼承自 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> ，定義配置 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 內轉譯內容的屬性。</span><span class="sxs-lookup"><span data-stu-id="45354-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="45354-114">使用 :::no-loc(Razor)::: 語法 `@Body` 來指定配置標記中呈現內容的位置。</span><span class="sxs-lookup"><span data-stu-id="45354-114">Uses the :::no-loc(Razor)::: syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="45354-115">下列程式碼範例會顯示 :::no-loc(Razor)::: 版面配置元件的範本 `MainLayout.razor` 。</span><span class="sxs-lookup"><span data-stu-id="45354-115">The following code sample shows the :::no-loc(Razor)::: template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="45354-116">版面配置會繼承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 並設定 `@Body` 導覽列和頁尾之間的：</span><span class="sxs-lookup"><span data-stu-id="45354-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="45354-117">`MainLayout` 元件</span><span class="sxs-lookup"><span data-stu-id="45354-117">`MainLayout` component</span></span>

<span data-ttu-id="45354-118">在以其中一個專案範本為基礎的應用程式中 :::no-loc(Blazor)::: ， `MainLayout` (`MainLayout.razor`) 元件位於應用程式的 `Shared` 資料夾中：</span><span class="sxs-lookup"><span data-stu-id="45354-118">In an app based on one of the :::no-loc(Blazor)::: project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="45354-119">預設配置</span><span class="sxs-lookup"><span data-stu-id="45354-119">Default layout</span></span>

<span data-ttu-id="45354-120">在應用程式檔案的元件中指定預設的應用程式版面配置 <xref:Microsoft.AspNetCore.Components.Routing.Router> `App.razor` 。</span><span class="sxs-lookup"><span data-stu-id="45354-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="45354-121">下列 <xref:Microsoft.AspNetCore.Components.Routing.Router> 元件是由預設範本所提供，會 :::no-loc(Blazor)::: 將預設版面配置設定為 `MainLayout` 元件：</span><span class="sxs-lookup"><span data-stu-id="45354-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default :::no-loc(Blazor)::: templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="45354-122">若要為內容提供預設版面配置 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> ，請 <xref:Microsoft.AspNetCore.Components.LayoutView> 指定 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容的：</span><span class="sxs-lookup"><span data-stu-id="45354-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="45354-123">如需元件的詳細資訊 <xref:Microsoft.AspNetCore.Components.Routing.Router> ，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="45354-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="45354-124">將配置指定為路由器中的預設配置，是很有用的作法，因為它可以覆寫每個元件或每個資料夾。</span><span class="sxs-lookup"><span data-stu-id="45354-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="45354-125">偏好使用路由器來設定應用程式的預設版面配置，因為這是最常見的技巧。</span><span class="sxs-lookup"><span data-stu-id="45354-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="45354-126">指定元件中的版面配置</span><span class="sxs-lookup"><span data-stu-id="45354-126">Specify a layout in a component</span></span>

<span data-ttu-id="45354-127">使用指示詞將版面配置套用 :::no-loc(Razor)::: `@layout` 至元件。</span><span class="sxs-lookup"><span data-stu-id="45354-127">Use the :::no-loc(Razor)::: directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="45354-128">編譯器會將套用 `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> 至元件類別的轉換成。</span><span class="sxs-lookup"><span data-stu-id="45354-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="45354-129">下列元件的內容 `MasterList` 會插入到的 `MasterLayout` 位置 `@Body` ：</span><span class="sxs-lookup"><span data-stu-id="45354-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="45354-130">直接在元件中指定版面配置，會覆寫路由器中的 *預設* 組態集，或從匯入的指示詞 `@layout` `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="45354-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="45354-131">集中式版面配置選取</span><span class="sxs-lookup"><span data-stu-id="45354-131">Centralized layout selection</span></span>

<span data-ttu-id="45354-132">應用程式的每個資料夾都可以選擇性地包含名為的範本檔案 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="45354-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="45354-133">編譯器包含在相同資料夾中所有範本的 imports 檔中所指定的指示詞 :::no-loc(Razor)::: ，並以遞迴方式在其所有子資料夾中包含這些指令程式。</span><span class="sxs-lookup"><span data-stu-id="45354-133">The compiler includes the directives specified in the imports file in all of the :::no-loc(Razor)::: templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="45354-134">因此， `_Imports.razor` 包含的檔案 `@layout MyCoolLayout` 可確保資料夾中的所有元件都使用 `MyCoolLayout` 。</span><span class="sxs-lookup"><span data-stu-id="45354-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="45354-135">不需要重複新增 `@layout MyCoolLayout` 至 `.razor` 資料夾和子資料夾內的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="45354-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="45354-136">`@using` 指示詞也會以同樣的方式套用至元件。</span><span class="sxs-lookup"><span data-stu-id="45354-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="45354-137">下列檔案會匯 `_Imports.razor` 入：</span><span class="sxs-lookup"><span data-stu-id="45354-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="45354-138">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="45354-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="45354-139">:::no-loc(Razor):::相同資料夾和任何子資料夾中的所有元件。</span><span class="sxs-lookup"><span data-stu-id="45354-139">All :::no-loc(Razor)::: components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="45354-140">`:::no-loc(Blazor):::App1.Data` 命名空間。</span><span class="sxs-lookup"><span data-stu-id="45354-140">The `:::no-loc(Blazor):::App1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="45354-141">檔案 `_Imports.razor` 類似于 [ :::no-loc(Razor)::: views 和 pages 的 _ViewImports cshtml](xref:mvc/views/layout#importing-shared-directives) 檔案，但特別適用于 :::no-loc(Razor)::: 元件檔。</span><span class="sxs-lookup"><span data-stu-id="45354-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for :::no-loc(Razor)::: views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to :::no-loc(Razor)::: component files.</span></span>

<span data-ttu-id="45354-142">在中指定配置會 `_Imports.razor` 覆寫指定為路由器 *預設版面* 配置的版面配置。</span><span class="sxs-lookup"><span data-stu-id="45354-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="45354-143">請勿 **not** 將指示詞新增 :::no-loc(Razor)::: `@layout` 至根檔案 `_Imports.razor` ，這會導致應用程式中的版面配置無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="45354-143">Do **not** add a :::no-loc(Razor)::: `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="45354-144">若要控制預設的應用程式版面配置，請在元件中指定版面配置 `Router` 。</span><span class="sxs-lookup"><span data-stu-id="45354-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="45354-145">如需詳細資訊，請參閱 [預設版面](#default-layout) 配置區段。</span><span class="sxs-lookup"><span data-stu-id="45354-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="45354-146">嵌套版面配置</span><span class="sxs-lookup"><span data-stu-id="45354-146">Nested layouts</span></span>

<span data-ttu-id="45354-147">應用程式可由嵌套配置組成。</span><span class="sxs-lookup"><span data-stu-id="45354-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="45354-148">元件可以參考配置，進而參考另一個版面配置。</span><span class="sxs-lookup"><span data-stu-id="45354-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="45354-149">例如，使用嵌套配置來建立多層式功能表結構。</span><span class="sxs-lookup"><span data-stu-id="45354-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="45354-150">下列範例示範如何使用嵌套版面配置。</span><span class="sxs-lookup"><span data-stu-id="45354-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="45354-151">檔案 `EpisodesComponent.razor` 是要顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="45354-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="45354-152">元件會參考 `MasterListLayout` ：</span><span class="sxs-lookup"><span data-stu-id="45354-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="45354-153">檔案會 `MasterListLayout.razor` 提供 `MasterListLayout` 。</span><span class="sxs-lookup"><span data-stu-id="45354-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="45354-154">版面配置會參考另一個版面配置， `MasterLayout` 也就是轉譯的位置。</span><span class="sxs-lookup"><span data-stu-id="45354-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="45354-155">`EpisodesComponent` 會呈現出現的位置 `@Body` ：</span><span class="sxs-lookup"><span data-stu-id="45354-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="45354-156">最後， `MasterLayout` 在中 `MasterLayout.razor` 包含最上層的版面配置元素，例如頁首、主功能表和頁尾。</span><span class="sxs-lookup"><span data-stu-id="45354-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="45354-157">`MasterListLayout` 使用 `EpisodesComponent` 呈現時，會 `@Body` 出現：</span><span class="sxs-lookup"><span data-stu-id="45354-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="45354-158">:::no-loc(Razor):::使用整合元件共用頁面配置</span><span class="sxs-lookup"><span data-stu-id="45354-158">Share a :::no-loc(Razor)::: Pages layout with integrated components</span></span>

<span data-ttu-id="45354-159">當可路由的元件整合到 :::no-loc(Razor)::: 頁面應用程式時，應用程式的共用配置可以與元件一起使用。</span><span class="sxs-lookup"><span data-stu-id="45354-159">When routable components are integrated into a :::no-loc(Razor)::: Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="45354-160">如需詳細資訊，請參閱<xref:blazor/components/prerendering-and-integration>。</span><span class="sxs-lookup"><span data-stu-id="45354-160">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="45354-161">其他資源</span><span class="sxs-lookup"><span data-stu-id="45354-161">Additional resources</span></span>

* <xref:mvc/views/layout>
