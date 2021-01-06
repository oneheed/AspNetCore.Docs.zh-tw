---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 瞭解如何管理應用程式中的要求路由，以及如何在應用程式中使用 NavLink 元件 Blazor 進行導覽。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: ec183f4aadc6bafd8e77f9d97291ba3d47bd92f5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506925"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="b31c1-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="b31c1-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="b31c1-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b31c1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b31c1-105">在本文中，您將瞭解如何管理要求路由，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件在應用程式中建立流覽連結 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="b31c1-106">路由範本</span><span class="sxs-lookup"><span data-stu-id="b31c1-106">Route templates</span></span>

<span data-ttu-id="b31c1-107"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件可讓您路由傳送至 Razor 應用程式中的元件 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="b31c1-108"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會用於 `App` Blazor 應用程式的元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="b31c1-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="b31c1-110">當編譯具有指示詞的 Razor 元件 (`.razor`) [ `@page` 時](xref:mvc/views/razor#page)，會提供所產生的元件類別來 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 指定元件的路由範本。</span><span class="sxs-lookup"><span data-stu-id="b31c1-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="b31c1-111">當應用程式啟動時，會掃描指定為路由器的元件， `AppAssembly` 以收集具有的應用程式元件的路由資訊 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="b31c1-112">在執行時間， <xref:Microsoft.AspNetCore.Components.RouteView> 元件：</span><span class="sxs-lookup"><span data-stu-id="b31c1-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="b31c1-113">從接收， <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> 連同任何路由參數。</span><span class="sxs-lookup"><span data-stu-id="b31c1-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="b31c1-114">以其配置呈現指定的元件，包括任何 [進一步的嵌套](xref:blazor/layouts)版面配置。</span><span class="sxs-lookup"><span data-stu-id="b31c1-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="b31c1-115">選擇性地 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 針對未使用指示詞指定配置的元件，使用配置類別來指定[ `@layout` ](xref:blazor/layouts#specify-a-layout-in-a-component)參數。</span><span class="sxs-lookup"><span data-stu-id="b31c1-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="b31c1-116">架構的 Blazor 專案範本會將 `MainLayout` 元件 (`Shared/MainLayout.razor`) 指定為應用程式的預設版面配置。</span><span class="sxs-lookup"><span data-stu-id="b31c1-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="b31c1-117">如需版面配置的詳細資訊，請參閱 <xref:blazor/layouts> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="b31c1-118">元件支援使用多個指示詞[ `@page` 的多個路由](xref:mvc/views/razor#page)範本。</span><span class="sxs-lookup"><span data-stu-id="b31c1-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="b31c1-119">下列範例元件會載入和的要求 `/BlazorRoute` `/DifferentBlazorRoute` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="b31c1-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="b31c1-121">若要正確解析 Url，應用程式必須在其檔案中包含標籤 `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 以及屬性中指定的應用程式基底路徑 `href` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="b31c1-122">如需詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="b31c1-123">在找不到內容時提供自訂內容</span><span class="sxs-lookup"><span data-stu-id="b31c1-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="b31c1-124"><xref:Microsoft.AspNetCore.Components.Routing.Router>如果找不到要求的路由內容，此元件可讓應用程式指定自訂內容。</span><span class="sxs-lookup"><span data-stu-id="b31c1-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="b31c1-125">在元件的元件 `App` 範本中，設定自訂內容 <xref:Microsoft.AspNetCore.Components.Routing.Router> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="b31c1-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="b31c1-127">以標記的內容支援任意專案 `<NotFound>` ，例如其他互動式元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="b31c1-128">若要將預設版面配置套用至 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容，請參閱 <xref:blazor/layouts#default-layout> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="b31c1-129">從多個元件路由至元件</span><span class="sxs-lookup"><span data-stu-id="b31c1-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="b31c1-130">使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 參數來指定在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 搜尋可路由元件時，要考慮的元件的其他元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="b31c1-131">除了指定的元件之外，還會掃描額外的元件 `AppAssembly` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="b31c1-132">在下列範例中， `Component1` 是在參考 [元件類別庫](xref:blazor/components/class-libraries)中定義的可路由元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="b31c1-133">下列 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 範例會產生的路由支援 `Component1` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="b31c1-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="b31c1-135">路由參數</span><span class="sxs-lookup"><span data-stu-id="b31c1-135">Route parameters</span></span>

<span data-ttu-id="b31c1-136">路由器會使用路由參數，以相同的名稱填入對應的 [元件參數](xref:blazor/components/index#component-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="b31c1-137">路由參數名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="b31c1-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="b31c1-138">在下列範例中，參數會將 `text` 路由區段的值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b31c1-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b31c1-139">當提出要求時 `/RouteParameter/amazing` ， `<h1>` 標記內容會轉譯為 `Blazor is amazing!` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="b31c1-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b31c1-141">支援選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="b31c1-141">Optional parameters are supported.</span></span> <span data-ttu-id="b31c1-142">在下列範例中， `text` 選擇性參數會將路由區段的值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b31c1-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b31c1-143">如果區段不存在，的值 `Text` 會設定為 `fantastic` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b31c1-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b31c1-145">不支援選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="b31c1-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="b31c1-146">下列範例會套用兩個[ `@page` ](xref:mvc/views/razor#page)指示詞。</span><span class="sxs-lookup"><span data-stu-id="b31c1-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="b31c1-147">第一個指示詞允許在不使用參數的情況下流覽至元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b31c1-148">第二個指示詞會將 `{text}` 路由參數值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b31c1-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="b31c1-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b31c1-150">使用 on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) （而不是） [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 允許使用不同的選擇性參數值，將應用程式流覽至相同的元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="b31c1-151">根據上述範例， `OnParametersSet` 當使用者應該要能夠從移至或移至時，請使用 `/RouteParameter` `/RouteParameter/amazing` `/RouteParameter/amazing` `/RouteParameter` ：</span><span class="sxs-lookup"><span data-stu-id="b31c1-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="b31c1-152">路由條件約束</span><span class="sxs-lookup"><span data-stu-id="b31c1-152">Route constraints</span></span>

<span data-ttu-id="b31c1-153">路由條件約束會強制將路由區段上的類型比對至元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="b31c1-154">在下列範例中，元件的路由 `User` 只會符合下列條件：</span><span class="sxs-lookup"><span data-stu-id="b31c1-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="b31c1-155">`Id`要求 URL 中有路由區段。</span><span class="sxs-lookup"><span data-stu-id="b31c1-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="b31c1-156">`Id`區段是 (`int`) 類型的整數。</span><span class="sxs-lookup"><span data-stu-id="b31c1-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="b31c1-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="b31c1-158">下表中顯示的路由條件約束為可用。</span><span class="sxs-lookup"><span data-stu-id="b31c1-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="b31c1-159">針對符合不因文化特性而異的路由條件約束，請參閱下表中的警告以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="b31c1-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="b31c1-160">條件約束</span><span class="sxs-lookup"><span data-stu-id="b31c1-160">Constraint</span></span> | <span data-ttu-id="b31c1-161">範例</span><span class="sxs-lookup"><span data-stu-id="b31c1-161">Example</span></span>           | <span data-ttu-id="b31c1-162">範例相符項目</span><span class="sxs-lookup"><span data-stu-id="b31c1-162">Example Matches</span></span>                                                                  | <span data-ttu-id="b31c1-163">非變異值</span><span class="sxs-lookup"><span data-stu-id="b31c1-163">Invariant</span></span><br><span data-ttu-id="b31c1-164">culture</span><span class="sxs-lookup"><span data-stu-id="b31c1-164">culture</span></span><br><span data-ttu-id="b31c1-165">比對</span><span class="sxs-lookup"><span data-stu-id="b31c1-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="b31c1-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="b31c1-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="b31c1-167">否</span><span class="sxs-lookup"><span data-stu-id="b31c1-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="b31c1-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="b31c1-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="b31c1-169">是</span><span class="sxs-lookup"><span data-stu-id="b31c1-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="b31c1-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="b31c1-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="b31c1-171">是</span><span class="sxs-lookup"><span data-stu-id="b31c1-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="b31c1-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b31c1-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b31c1-173">是</span><span class="sxs-lookup"><span data-stu-id="b31c1-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="b31c1-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b31c1-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b31c1-175">是</span><span class="sxs-lookup"><span data-stu-id="b31c1-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="b31c1-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="b31c1-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="b31c1-177">否</span><span class="sxs-lookup"><span data-stu-id="b31c1-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="b31c1-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b31c1-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b31c1-179">是</span><span class="sxs-lookup"><span data-stu-id="b31c1-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="b31c1-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b31c1-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b31c1-181">是</span><span class="sxs-lookup"><span data-stu-id="b31c1-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="b31c1-182">確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 <xref:System.DateTime>) 一律使用不因國別而異的文化特性。</span><span class="sxs-lookup"><span data-stu-id="b31c1-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="b31c1-183">這些條件約束假設 URL 不可當地語系化。</span><span class="sxs-lookup"><span data-stu-id="b31c1-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="b31c1-184">使用包含點的 Url 路由傳送</span><span class="sxs-lookup"><span data-stu-id="b31c1-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="b31c1-185">針對託管 Blazor WebAssembly 和 Blazor Server 應用程式，伺服器端的預設路由範本會假設要求 URL 的最後一個區段包含點 (`.`) 要求的檔案。</span><span class="sxs-lookup"><span data-stu-id="b31c1-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="b31c1-186">例如，路由器會將 URL 解釋為名為之檔案的 `https://localhost.com:5001/example/some.thing` 要求 `some.thing` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="b31c1-187">如果您想要使用指示詞 `some.thing` 路由傳送至元件， [ `@page` 而不](xref:mvc/views/razor#page) `some.thing` 是路由參數值，則應用程式不需要額外設定，就會傳回 404-找不到的回應。</span><span class="sxs-lookup"><span data-stu-id="b31c1-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="b31c1-188">若要使用具有一個或多個包含點之參數的路由，應用程式必須使用自訂範本來設定路由。</span><span class="sxs-lookup"><span data-stu-id="b31c1-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="b31c1-189">請考慮下列 `Example` 可從 URL 最後一個區段接收路由參數的元件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="b31c1-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b31c1-191">若要允許 *`Server`* 託管解決方案的應用程式 Blazor WebAssembly 使用 route 參數中的點來路由傳送要求 `param` ，請在中新增具有選擇性參數的回溯檔案路由範本 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b31c1-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="b31c1-193">若要將 Blazor Server 應用程式設定為使用路由參數中的點來路由傳送要求 `param` ，請在中新增具有選擇性參數的回溯頁面路由範本 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b31c1-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="b31c1-195">如需詳細資訊，請參閱 <xref:fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="b31c1-196">Catch-all 路由參數</span><span class="sxs-lookup"><span data-stu-id="b31c1-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b31c1-197">元件中支援攔截所有路由參數，這些參數會跨多個資料夾界限來捕捉路徑。</span><span class="sxs-lookup"><span data-stu-id="b31c1-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="b31c1-198">Catch-all 路由參數為：</span><span class="sxs-lookup"><span data-stu-id="b31c1-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="b31c1-199">命名為與路由區段名稱相符。</span><span class="sxs-lookup"><span data-stu-id="b31c1-199">Named to match the route segment name.</span></span> <span data-ttu-id="b31c1-200">命名不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="b31c1-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="b31c1-201">`string` 型別。</span><span class="sxs-lookup"><span data-stu-id="b31c1-201">A `string` type.</span></span> <span data-ttu-id="b31c1-202">架構不會提供自動轉換。</span><span class="sxs-lookup"><span data-stu-id="b31c1-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="b31c1-203">在 URL 的結尾。</span><span class="sxs-lookup"><span data-stu-id="b31c1-203">At the end of the URL.</span></span>

<span data-ttu-id="b31c1-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="b31c1-205">`/catch-all/this/is/a/test`若為具有路由範本的 URL `/catch-all/{*pageRoute}` ，的值 `PageRoute` 會設定為 `this/is/a/test` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="b31c1-206">已解碼之已捕捉路徑的斜線和區段。</span><span class="sxs-lookup"><span data-stu-id="b31c1-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="b31c1-207">若為的路由範本 `/catch-all/{*pageRoute}` ，URL 會 `/catch-all/this/is/a%2Ftest%2A` 產生 `this/is/a/test*` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b31c1-208">ASP.NET Core 5.0 或更新版本中支援 Catch-all 路由參數。</span><span class="sxs-lookup"><span data-stu-id="b31c1-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="b31c1-209">如需詳細資訊，請選取此文章的5.0 版本。</span><span class="sxs-lookup"><span data-stu-id="b31c1-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="b31c1-210">URI 和流覽狀態協助程式</span><span class="sxs-lookup"><span data-stu-id="b31c1-210">URI and navigation state helpers</span></span>

<span data-ttu-id="b31c1-211">用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 來管理 c # 程式碼中的 uri 和導覽。</span><span class="sxs-lookup"><span data-stu-id="b31c1-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="b31c1-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="b31c1-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="b31c1-213">member</span><span class="sxs-lookup"><span data-stu-id="b31c1-213">Member</span></span> | <span data-ttu-id="b31c1-214">描述</span><span class="sxs-lookup"><span data-stu-id="b31c1-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="b31c1-215">取得目前的絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="b31c1-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="b31c1-216">取得基底 URI (，其尾端斜線) 可在相對 URI 路徑前面加上，以產生絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="b31c1-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="b31c1-217">一般而言，會 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 對應至 `href` `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 中檔元素上的屬性 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="b31c1-218">流覽至指定的 URI。</span><span class="sxs-lookup"><span data-stu-id="b31c1-218">Navigates to the specified URI.</span></span> <span data-ttu-id="b31c1-219">如果 `forceLoad` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="b31c1-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="b31c1-220">略過用戶端路由。</span><span class="sxs-lookup"><span data-stu-id="b31c1-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="b31c1-221">因為 URI 通常是由用戶端路由器處理，所以會強制瀏覽器從伺服器載入新的頁面。</span><span class="sxs-lookup"><span data-stu-id="b31c1-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="b31c1-222">當導覽位置變更時引發的事件。</span><span class="sxs-lookup"><span data-stu-id="b31c1-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="b31c1-223">將相對 URI 轉換為絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="b31c1-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="b31c1-224">假設基底 URI (例如，) 先前傳回的 URI <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ，會將絕對 uri 轉換成相對於基底 uri 前置詞的 uri。</span><span class="sxs-lookup"><span data-stu-id="b31c1-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="b31c1-225">針對 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 事件， <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 提供下列有關導覽事件的資訊：</span><span class="sxs-lookup"><span data-stu-id="b31c1-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="b31c1-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="b31c1-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="b31c1-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果 `true` ， Blazor 從瀏覽器攔截導覽。</span><span class="sxs-lookup"><span data-stu-id="b31c1-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="b31c1-228">如果 `false` 為，則 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 會造成導覽。</span><span class="sxs-lookup"><span data-stu-id="b31c1-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="b31c1-229">下列元件：</span><span class="sxs-lookup"><span data-stu-id="b31c1-229">The following component:</span></span>

* <span data-ttu-id="b31c1-230">使用選取按鈕時，流覽至應用程式的 `Counter` 元件 (`Pages/Counter.razor`) <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="b31c1-231">藉由訂閱來處理位置已變更的事件 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b31c1-232">`HandleLocationChanged`當架構呼叫時，方法會解除掛鉤 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="b31c1-233">Unhooking 方法允許元件的垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="b31c1-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="b31c1-234">當選取按鈕時，記錄器執行會記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="b31c1-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="b31c1-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="b31c1-236">如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="b31c1-237">查詢字串和剖析參數</span><span class="sxs-lookup"><span data-stu-id="b31c1-237">Query string and parse parameters</span></span>

<span data-ttu-id="b31c1-238">您可以從屬性取得要求的查詢字串 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="b31c1-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="b31c1-239">剖析查詢字串的參數：</span><span class="sxs-lookup"><span data-stu-id="b31c1-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="b31c1-240">應用程式可以使用 <xref:Microsoft.AspNetCore.WebUtilities> API。</span><span class="sxs-lookup"><span data-stu-id="b31c1-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="b31c1-241">如果應用程式無法使用 API，請在應用程式的專案檔中新增 [AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)的套件參考。</span><span class="sxs-lookup"><span data-stu-id="b31c1-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="b31c1-242">使用剖析查詢字串之後，取得值 <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b31c1-243">下列 `ParseQueryString` 元件範例會剖析名為的查詢字串參數索引鍵 `ship` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="b31c1-244">例如，URL 查詢字串索引鍵/值組會 `?ship=Tardis` `Tardis` 在中捕捉值 `queryValue` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="b31c1-245">針對下列範例，請使用 URL 流覽至應用程式 `https://localhost:5001/parse-query-string?ship=Tardis` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="b31c1-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="b31c1-247">`NavLink` 元件</span><span class="sxs-lookup"><span data-stu-id="b31c1-247">`NavLink` component</span></span>

<span data-ttu-id="b31c1-248"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>建立導覽連結時，請使用元件來取代 HTML 超連結元素 (`<a>`) 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="b31c1-249"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>元件的行為類似專案 `<a>` ，但它會 `active` 根據其是否 `href` 符合目前的 URL 來切換 CSS 類別。</span><span class="sxs-lookup"><span data-stu-id="b31c1-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="b31c1-250">`active`類別可協助使用者瞭解在顯示的導覽連結中，哪個頁面是使用中的頁面。</span><span class="sxs-lookup"><span data-stu-id="b31c1-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="b31c1-251">（選擇性）將 CSS 類別名稱指派為， <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> 以便在目前的路由符合時將自訂 css 類別套用至轉譯的連結 `href` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="b31c1-252">下列 `NavMenu` 元件 [`Bootstrap`](https://getbootstrap.com/docs/) 會建立示範如何使用元件的巡覽列 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ：</span><span class="sxs-lookup"><span data-stu-id="b31c1-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b31c1-253">`NavMenu`元件 (`NavMenu.razor`) 會提供在 `Shared` 專案範本所產生之應用程式的資料夾中 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="b31c1-254">有兩個 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 選項可供您指派給 `Match` 元素的屬性 `<NavLink>` ：</span><span class="sxs-lookup"><span data-stu-id="b31c1-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="b31c1-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當符合整個目前的 URL 時，會處於作用中狀態。</span><span class="sxs-lookup"><span data-stu-id="b31c1-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="b31c1-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*預設*) ： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當它符合目前 URL 的任何前置詞時，就會處於作用中狀態。</span><span class="sxs-lookup"><span data-stu-id="b31c1-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="b31c1-257">在上述範例中，Home 會 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 符合 home URL，而且只會 `active` 在應用程式的預設基底路徑 URL 上接收 CSS 類別 (例如 `https://localhost:5001/`) 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="b31c1-258">第二個會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` 使用者造訪具有前置詞 (的任何 URL 時收到類別 `component` ，例如， `https://localhost:5001/component` 以及 `https://localhost:5001/component/another-segment`) 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="b31c1-259">其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件屬性會傳遞至轉譯的錨點標記。</span><span class="sxs-lookup"><span data-stu-id="b31c1-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="b31c1-260">在下列範例中， <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件包含 `target` 屬性：</span><span class="sxs-lookup"><span data-stu-id="b31c1-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="b31c1-261">下列 HTML 標籤會呈現：</span><span class="sxs-lookup"><span data-stu-id="b31c1-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="b31c1-262">由於轉譯 Blazor 子內容的方式， `NavLink` 如果在 `for` `NavLink` (子) 元件的內容中使用遞增迴圈變數，則迴圈內的轉譯元件需要本機索引變數：</span><span class="sxs-lookup"><span data-stu-id="b31c1-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="b31c1-263">在此案例中使用索引變數，是在其子 [內容](xref:blazor/components/index#child-content)中使用迴圈變數的 **任何** 子元件（而不只是元件）的需求 `NavLink` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="b31c1-264">或者，使用 `foreach` 迴圈搭配 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="b31c1-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="b31c1-265">ASP.NET Core 端點路由整合</span><span class="sxs-lookup"><span data-stu-id="b31c1-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="b31c1-266">*本節僅適用于 Blazor Server 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="b31c1-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="b31c1-267">Blazor Server 已整合至 [ASP.NET Core 端點路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="b31c1-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="b31c1-268">ASP.NET Core 應用程式設定為在中接受互動式元件的連入 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 連線 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="b31c1-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b31c1-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="b31c1-270">一般設定是將所有要求路由傳送至 Razor 頁面，作為應用程式的伺服器端部分的主機 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="b31c1-271">依照慣例， *主機* 頁面通常會 `_Host.cshtml` 在 `Pages` 應用程式的資料夾中命名。</span><span class="sxs-lookup"><span data-stu-id="b31c1-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="b31c1-272">主機檔案中指定的路由稱為「回溯 *路由* 」，因為它在路由比對中具有低優先順序。</span><span class="sxs-lookup"><span data-stu-id="b31c1-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="b31c1-273">當其他路由不相符時，就會使用 fallback 路由。</span><span class="sxs-lookup"><span data-stu-id="b31c1-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="b31c1-274">這可讓應用程式使用其他控制器和頁面，而不會干擾應用程式中的元件路由 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="b31c1-275">如需針對 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 非根目錄 URL 伺服器裝載設定的詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="b31c1-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
