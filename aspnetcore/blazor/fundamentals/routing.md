---
title: ASP.NET 核心 Blazor 路由
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
ms.openlocfilehash: ee6de9a13a69154eef6b677663091667d391452f
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395054"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="e2cb4-103">ASP.NET 核心 Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="e2cb4-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="e2cb4-104">在本文中，您將瞭解如何管理要求路由，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件在應用程式中建立流覽連結 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-104">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="e2cb4-105">路由範本</span><span class="sxs-lookup"><span data-stu-id="e2cb4-105">Route templates</span></span>

<span data-ttu-id="e2cb4-106"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件可讓您路由傳送至 Razor 應用程式中的元件 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-106">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="e2cb4-107"><xref:Microsoft.AspNetCore.Components.Routing.Router>元件會用於 `App` Blazor 應用程式的元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="e2cb4-108">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-108">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

<span data-ttu-id="e2cb4-109">當編譯具有指示詞的 Razor 元件 (`.razor`) [ `@page` 時](xref:mvc/views/razor#page)，會提供所產生的元件類別來 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 指定元件的路由範本。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-109">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="e2cb4-110">當應用程式啟動時，會掃描指定為路由器的元件， `AppAssembly` 以收集具有的應用程式元件的路由資訊 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-110">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="e2cb4-111">在執行時間， <xref:Microsoft.AspNetCore.Components.RouteView> 元件：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-111">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="e2cb4-112">從接收， <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> 連同任何路由參數。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-112">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="e2cb4-113">以其配置呈現指定的元件，包括任何 [進一步的嵌套](xref:blazor/layouts)版面配置。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-113">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="e2cb4-114">選擇性地 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 針對未使用指示詞指定配置的元件，使用配置類別來指定[ `@layout` ](xref:blazor/layouts#apply-a-layout-to-a-component)參數。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-114">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#apply-a-layout-to-a-component).</span></span> <span data-ttu-id="e2cb4-115">架構的[ Blazor 專案範本](xref:blazor/project-structure)會將 `MainLayout` 元件 (`Shared/MainLayout.razor`) 指定為應用程式的預設版面配置。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-115">The framework's [Blazor project templates](xref:blazor/project-structure) specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="e2cb4-116">如需版面配置的詳細資訊，請參閱 <xref:blazor/layouts> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-116">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="e2cb4-117">元件支援使用多個指示詞[ `@page` 的多個路由](xref:mvc/views/razor#page)範本。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-117">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="e2cb4-118">下列範例元件會載入和的要求 `/BlazorRoute` `/DifferentBlazorRoute` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-118">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="e2cb4-119">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-119">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="e2cb4-120">若要正確解析 Url，應用程式必須在其檔案中包含標籤 `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 以及屬性中指定的應用程式基底路徑 `href` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-120">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="e2cb4-121">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-121">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="e2cb4-122">在找不到內容時提供自訂內容</span><span class="sxs-lookup"><span data-stu-id="e2cb4-122">Provide custom content when content isn't found</span></span>

<span data-ttu-id="e2cb4-123"><xref:Microsoft.AspNetCore.Components.Routing.Router>如果找不到要求的路由內容，此元件可讓應用程式指定自訂內容。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-123">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="e2cb4-124">在元件的元件 `App` 範本中，設定自訂內容 <xref:Microsoft.AspNetCore.Components.Routing.Router> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-124">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="e2cb4-125">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-125">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="e2cb4-126">以標記的內容支援任意專案 `<NotFound>` ，例如其他互動式元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-126">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="e2cb4-127">若要將預設版面配置套用至 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容，請參閱 <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-127">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="e2cb4-128">從多個元件路由至元件</span><span class="sxs-lookup"><span data-stu-id="e2cb4-128">Route to components from multiple assemblies</span></span>

<span data-ttu-id="e2cb4-129">使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 參數來指定在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 搜尋可路由元件時，要考慮的元件的其他元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-129">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="e2cb4-130">除了指定的元件之外，還會掃描額外的元件 `AppAssembly` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-130">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="e2cb4-131">在下列範例中， `Component1` 是在參考 [元件類別庫](xref:blazor/components/class-libraries)中定義的可路由元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-131">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="e2cb4-132">下列 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 範例會產生的路由支援 `Component1` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-132">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="e2cb4-133">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-133">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="e2cb4-134">路由參數</span><span class="sxs-lookup"><span data-stu-id="e2cb4-134">Route parameters</span></span>

<span data-ttu-id="e2cb4-135">路由器會使用路由參數，以相同的名稱填入對應的 [元件參數](xref:blazor/components/index#component-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-135">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="e2cb4-136">路由參數名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-136">Route parameter names are case insensitive.</span></span> <span data-ttu-id="e2cb4-137">在下列範例中，參數會將 `text` 路由區段的值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-137">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="e2cb4-138">當提出要求時 `/RouteParameter/amazing` ， `<h1>` 標記內容會轉譯為 `Blazor is amazing!` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-138">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="e2cb4-139">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-139">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e2cb4-140">支援選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-140">Optional parameters are supported.</span></span> <span data-ttu-id="e2cb4-141">在下列範例中， `text` 選擇性參數會將路由區段的值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-141">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="e2cb4-142">如果區段不存在，的值 `Text` 會設定為 `fantastic` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-142">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="e2cb4-143">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-143">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e2cb4-144">不支援選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-144">Optional parameters aren't supported.</span></span> <span data-ttu-id="e2cb4-145">下列範例會套用兩個[ `@page` ](xref:mvc/views/razor#page)指示詞。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-145">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="e2cb4-146">第一個指示詞允許在不使用參數的情況下流覽至元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-146">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="e2cb4-147">第二個指示詞會將 `{text}` 路由參數值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-147">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="e2cb4-148">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-148">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="e2cb4-149">使用 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) 取代 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 來允許使用不同的選擇性參數值，將應用程式流覽至相同的元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="e2cb4-150">根據上述範例， `OnParametersSet` 當使用者應該要能夠從移至或移至時，請使用 `/RouteParameter` `/RouteParameter/amazing` `/RouteParameter/amazing` `/RouteParameter` ：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="e2cb4-151">路由條件約束</span><span class="sxs-lookup"><span data-stu-id="e2cb4-151">Route constraints</span></span>

<span data-ttu-id="e2cb4-152">路由條件約束會強制將路由區段上的類型比對至元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="e2cb4-153">在下列範例中，元件的路由 `User` 只會符合下列條件：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-153">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="e2cb4-154">`Id`要求 URL 中有路由區段。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-154">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="e2cb4-155">`Id`區段是 (`int`) 類型的整數。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-155">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="e2cb4-156">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-156">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="e2cb4-157">下表中顯示的路由條件約束為可用。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="e2cb4-158">針對符合不因文化特性而異的路由條件約束，請參閱下表中的警告以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-158">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="e2cb4-159">條件約束</span><span class="sxs-lookup"><span data-stu-id="e2cb4-159">Constraint</span></span> | <span data-ttu-id="e2cb4-160">範例</span><span class="sxs-lookup"><span data-stu-id="e2cb4-160">Example</span></span>           | <span data-ttu-id="e2cb4-161">範例相符項目</span><span class="sxs-lookup"><span data-stu-id="e2cb4-161">Example Matches</span></span>                                                                  | <span data-ttu-id="e2cb4-162">非變異值</span><span class="sxs-lookup"><span data-stu-id="e2cb4-162">Invariant</span></span><br><span data-ttu-id="e2cb4-163">culture</span><span class="sxs-lookup"><span data-stu-id="e2cb4-163">culture</span></span><br><span data-ttu-id="e2cb4-164">比對</span><span class="sxs-lookup"><span data-stu-id="e2cb4-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="e2cb4-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="e2cb4-166">否</span><span class="sxs-lookup"><span data-stu-id="e2cb4-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="e2cb4-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="e2cb4-168">是</span><span class="sxs-lookup"><span data-stu-id="e2cb4-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="e2cb4-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="e2cb4-170">是</span><span class="sxs-lookup"><span data-stu-id="e2cb4-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="e2cb4-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="e2cb4-172">是</span><span class="sxs-lookup"><span data-stu-id="e2cb4-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="e2cb4-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="e2cb4-174">是</span><span class="sxs-lookup"><span data-stu-id="e2cb4-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="e2cb4-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="e2cb4-176">否</span><span class="sxs-lookup"><span data-stu-id="e2cb4-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="e2cb4-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="e2cb4-178">是</span><span class="sxs-lookup"><span data-stu-id="e2cb4-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="e2cb4-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="e2cb4-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="e2cb4-180">是</span><span class="sxs-lookup"><span data-stu-id="e2cb4-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="e2cb4-181">確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 <xref:System.DateTime>) 一律使用不因國別而異的文化特性。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="e2cb4-182">這些條件約束假設 URL 不可當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-182">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="e2cb4-183">使用包含點的 Url 路由傳送</span><span class="sxs-lookup"><span data-stu-id="e2cb4-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="e2cb4-184">針對託管 Blazor WebAssembly 和 Blazor Server 應用程式，伺服器端的預設路由範本會假設要求 URL 的最後一個區段包含點 (`.`) 要求的檔案。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="e2cb4-185">例如，路由器會將 URL 解釋為名為之檔案的 `https://localhost.com:5001/example/some.thing` 要求 `some.thing` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-185">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="e2cb4-186">如果您想要使用指示詞 `some.thing` 路由傳送至元件， [ `@page` 而不](xref:mvc/views/razor#page) `some.thing` 是路由參數值，則應用程式不需要額外設定，就會傳回 404-找不到的回應。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-186">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="e2cb4-187">若要使用具有一個或多個包含點之參數的路由，應用程式必須使用自訂範本來設定路由。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-187">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="e2cb4-188">請考慮下列 `Example` 可從 URL 最後一個區段接收路由參數的元件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-188">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="e2cb4-189">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-189">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="e2cb4-190">若要允許 **`Server`** 託管解決方案的應用程式 Blazor WebAssembly 使用 route 參數中的點來路由傳送要求 `param` ，請在中新增具有選擇性參數的回溯檔案路由範本 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-190">To permit the **`Server`** app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="e2cb4-191">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-191">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="e2cb4-192">若要將 Blazor Server 應用程式設定為使用路由參數中的點來路由傳送要求 `param` ，請在中新增具有選擇性參數的回溯頁面路由範本 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-192">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="e2cb4-193">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-193">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="e2cb4-194">如需詳細資訊，請參閱<xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-194">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="e2cb4-195">Catch-all 路由參數</span><span class="sxs-lookup"><span data-stu-id="e2cb4-195">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e2cb4-196">元件中支援攔截所有路由參數，這些參數會跨多個資料夾界限來捕捉路徑。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-196">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="e2cb4-197">Catch-all 路由參數為：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-197">Catch-all route parameters are:</span></span>

* <span data-ttu-id="e2cb4-198">命名為與路由區段名稱相符。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-198">Named to match the route segment name.</span></span> <span data-ttu-id="e2cb4-199">命名不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-199">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="e2cb4-200">`string` 型別。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-200">A `string` type.</span></span> <span data-ttu-id="e2cb4-201">架構不會提供自動轉換。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-201">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="e2cb4-202">在 URL 的結尾。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-202">At the end of the URL.</span></span>

<span data-ttu-id="e2cb4-203">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-203">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

<span data-ttu-id="e2cb4-204">`/catch-all/this/is/a/test`若為具有路由範本的 URL `/catch-all/{*pageRoute}` ，的值 `PageRoute` 會設定為 `this/is/a/test` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-204">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="e2cb4-205">已解碼之已捕捉路徑的斜線和區段。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-205">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="e2cb4-206">若為的路由範本 `/catch-all/{*pageRoute}` ，URL 會 `/catch-all/this/is/a%2Ftest%2A` 產生 `this/is/a/test*` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-206">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e2cb4-207">ASP.NET Core 5.0 或更新版本支援攔截所有路由參數。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-207">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="e2cb4-208">如需詳細資訊，請選取此文章的5.0 版本。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-208">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="e2cb4-209">URI 和流覽狀態協助程式</span><span class="sxs-lookup"><span data-stu-id="e2cb4-209">URI and navigation state helpers</span></span>

<span data-ttu-id="e2cb4-210">用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 來管理 c # 程式碼中的 uri 和導覽。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="e2cb4-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="e2cb4-212">member</span><span class="sxs-lookup"><span data-stu-id="e2cb4-212">Member</span></span> | <span data-ttu-id="e2cb4-213">描述</span><span class="sxs-lookup"><span data-stu-id="e2cb4-213">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="e2cb4-214">取得目前的絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-214">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="e2cb4-215">取得基底 URI (，其尾端斜線) 可在相對 URI 路徑前面加上，以產生絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-215">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="e2cb4-216">一般而言，會 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 對應至 `href` `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 中檔元素上的屬性 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-216">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="e2cb4-217">流覽至指定的 URI。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-217">Navigates to the specified URI.</span></span> <span data-ttu-id="e2cb4-218">如果 `forceLoad` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-218">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="e2cb4-219">略過用戶端路由。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-219">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="e2cb4-220">因為 URI 通常是由用戶端路由器處理，所以會強制瀏覽器從伺服器載入新的頁面。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-220">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="e2cb4-221">當導覽位置變更時引發的事件。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-221">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="e2cb4-222">將相對 URI 轉換為絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-222">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="e2cb4-223">假設基底 URI (例如，) 先前傳回的 URI <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ，會將絕對 uri 轉換成相對於基底 uri 前置詞的 uri。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-223">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="e2cb4-224">針對 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 事件， <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 提供下列有關導覽事件的資訊：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-224">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="e2cb4-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="e2cb4-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果 `true` ， Blazor 從瀏覽器攔截導覽。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="e2cb4-227">如果 `false` 為，則 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 會造成導覽。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-227">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="e2cb4-228">下列元件：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-228">The following component:</span></span>

* <span data-ttu-id="e2cb4-229">使用選取按鈕時，流覽至應用程式的 `Counter` 元件 (`Pages/Counter.razor`) <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-229">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="e2cb4-230">藉由訂閱來處理位置已變更的事件 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-230">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="e2cb4-231">`HandleLocationChanged`當架構呼叫時，方法會解除掛鉤 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-231">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="e2cb4-232">Unhooking 方法允許元件的垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-232">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="e2cb4-233">當選取按鈕時，記錄器執行會記錄下列資訊：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-233">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="e2cb4-234">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-234">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

<span data-ttu-id="e2cb4-235">如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-235">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="e2cb4-236">查詢字串和剖析參數</span><span class="sxs-lookup"><span data-stu-id="e2cb4-236">Query string and parse parameters</span></span>

<span data-ttu-id="e2cb4-237">您可以從屬性取得要求的查詢字串 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-237">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="e2cb4-238">剖析查詢字串的參數：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-238">To parse a query string's parameters:</span></span>

* <span data-ttu-id="e2cb4-239">應用程式可以使用 <xref:Microsoft.AspNetCore.WebUtilities> API。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-239">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="e2cb4-240">如果應用程式無法使用 API，請在應用程式的專案檔中新增 [AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)的套件參考。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-240">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="e2cb4-241">使用剖析查詢字串之後，取得值 <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-241">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="e2cb4-242">下列 `ParseQueryString` 元件範例會剖析名為的查詢字串參數索引鍵 `ship` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-242">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="e2cb4-243">例如，URL 查詢字串索引鍵/值組會 `?ship=Tardis` `Tardis` 在中捕捉值 `queryValue` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-243">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="e2cb4-244">針對下列範例，請使用 URL 流覽至應用程式 `https://localhost:5001/parse-query-string?ship=Tardis` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-244">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="e2cb4-245">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-245">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="e2cb4-246">`NavLink` 和 `NavMenu` 元件</span><span class="sxs-lookup"><span data-stu-id="e2cb4-246">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="e2cb4-247"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>建立導覽連結時，請使用元件來取代 HTML 超連結元素 (`<a>`) 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-247">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="e2cb4-248"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>元件的行為類似專案 `<a>` ，但它會 `active` 根據其是否 `href` 符合目前的 URL 來切換 CSS 類別。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-248">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="e2cb4-249">`active`類別可協助使用者瞭解在顯示的導覽連結中，哪個頁面是使用中的頁面。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-249">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="e2cb4-250">（選擇性）將 CSS 類別名稱指派為， <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> 以便在目前的路由符合時將自訂 css 類別套用至轉譯的連結 `href` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-250">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="e2cb4-251">下列 `NavMenu` 元件 [`Bootstrap`](https://getbootstrap.com/docs/) 會建立示範如何使用元件的巡覽列 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-251">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e2cb4-252">`NavMenu`元件 (`NavMenu.razor`) 會提供在 `Shared` [ Blazor 專案範本](xref:blazor/project-structure)所產生之應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-252">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="e2cb4-253">有兩個 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 選項可供您指派給 `Match` 元素的屬性 `<NavLink>` ：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-253">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="e2cb4-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當符合整個目前的 URL 時，會處於作用中狀態。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="e2cb4-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*預設*) ： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當它符合目前 URL 的任何前置詞時，就會處於作用中狀態。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="e2cb4-256">在上述範例中，Home 會 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 符合 home URL，而且只會 `active` 在應用程式的預設基底路徑 URL 上接收 CSS 類別 (例如 `https://localhost:5001/`) 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-256">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="e2cb4-257">第二個會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` 使用者造訪具有前置詞 (的任何 URL 時收到類別 `component` ，例如， `https://localhost:5001/component` 以及 `https://localhost:5001/component/another-segment`) 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-257">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="e2cb4-258">其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件屬性會傳遞至轉譯的錨點標記。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-258">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="e2cb4-259">在下列範例中， <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件包含 `target` 屬性：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-259">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="e2cb4-260">下列 HTML 標籤會呈現：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-260">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="e2cb4-261">由於轉譯 Blazor 子內容的方式， `NavLink` 如果在 `for` `NavLink` (子) 元件的內容中使用遞增迴圈變數，則迴圈內的轉譯元件需要本機索引變數：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-261">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="e2cb4-262">在此案例中使用索引變數，是在其子 [內容](xref:blazor/components/index#child-content)中使用迴圈變數的 **任何** 子元件（而不只是元件）的需求 `NavLink` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-262">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="e2cb4-263">或者，使用 `foreach` 迴圈搭配 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="e2cb4-263">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="e2cb4-264">ASP.NET 核心端點路由整合</span><span class="sxs-lookup"><span data-stu-id="e2cb4-264">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="e2cb4-265">*本節僅適用于 Blazor Server 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="e2cb4-265">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="e2cb4-266">Blazor Server 已整合至 [ASP.NET Core 端點路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-266">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="e2cb4-267">ASP.NET Core 應用程式設定為在中接受互動式元件的連入 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 連線 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-267">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="e2cb4-268">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="e2cb4-268">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

<span data-ttu-id="e2cb4-269">一般設定是將所有要求路由傳送至 Razor 頁面，作為應用程式的伺服器端部分的主機 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-269">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="e2cb4-270">依照慣例， *主機* 頁面通常會 `_Host.cshtml` 在 `Pages` 應用程式的資料夾中命名。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-270">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="e2cb4-271">主機檔案中指定的路由稱為「回溯 *路由* 」，因為它在路由比對中具有低優先順序。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-271">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="e2cb4-272">當其他路由不相符時，就會使用 fallback 路由。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-272">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="e2cb4-273">這可讓應用程式使用其他控制器和頁面，而不會干擾應用程式中的元件路由 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-273">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="e2cb4-274">如需針對 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 非根目錄 URL 伺服器裝載設定的詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="e2cb4-274">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
