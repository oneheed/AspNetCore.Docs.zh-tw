---
title: ASP.NET Core 中的延遲載入元件Blazor WebAssembly
author: guardrex
description: 探索如何在 ASP.NET Core 應用程式中延遲載入元件 Blazor WebAssembly 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0ce03badccad4e06aa3c316580ab82be38a806c6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013368"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="1a0b3-103">ASP.NET Core 中的延遲載入元件Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1a0b3-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="1a0b3-104">By [Safia Abdalla](https://safia.rocks)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a0b3-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1a0b3-105">Blazor WebAssembly應用程式啟動效能可以藉由延後載入一些應用程式元件來改善，直到需要它們為止，這稱為「*延遲載入*」。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="1a0b3-106">例如，只有在使用者流覽至該元件時，才會將用來轉譯單一元件的元件設定為僅載入。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="1a0b3-107">載入之後，元件會快取用戶端，並可供所有未來的導覽使用。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="1a0b3-108">Blazor的「消極式載入」功能可讓您將應用程式元件標示為消極式載入，這會在使用者流覽至特定路由時，于執行時間載入元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="1a0b3-109">此功能包含專案檔的變更，以及應用程式路由器的變更。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="1a0b3-110">元件消極式載入不會讓應用程式受益 Blazor Server ，因為元件不會下載至應用程式中的用戶端 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="1a0b3-111">專案檔</span><span class="sxs-lookup"><span data-stu-id="1a0b3-111">Project file</span></span>

<span data-ttu-id="1a0b3-112">在應用程式的專案檔中，將延遲載入的元件標示 (`.csproj` 使用 `BlazorWebAssemblyLazyLoad` 專案) 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="1a0b3-113">請使用不含副檔名的元件名稱 `.dll` 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="1a0b3-114">Blazor架構會防止這個專案群組所指定的元件在應用程式啟動時載入。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="1a0b3-115">下列範例會將大型自訂群組件標示 `GrantImaharaRobotControls.dll` 為延遲載入 () 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="1a0b3-116">如果標示為消極式載入的元件有相依性，則也必須在專案檔中將它們標示為延遲載入。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="1a0b3-117">只有應用程式所使用的元件可以延遲載入。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="1a0b3-118">連結器會從已發行的輸出中去除未使用的元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="1a0b3-119">`Router` 元件</span><span class="sxs-lookup"><span data-stu-id="1a0b3-119">`Router` component</span></span>

<span data-ttu-id="1a0b3-120">Blazor的 `Router` 元件會指定哪些元件會 Blazor 搜尋可路由的元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="1a0b3-121">`Router`元件也會負責呈現使用者導覽之路由的元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="1a0b3-122">`Router`元件支援 `OnNavigateAsync` 可與消極式載入搭配使用的功能。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="1a0b3-123">在應用程式的 `Router` 元件 (`App.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="1a0b3-124">新增 `OnNavigateAsync` 回呼。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="1a0b3-125">`OnNavigateAsync`當使用者執行下列動作時，就會叫用處理程式：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="1a0b3-126">第一次造訪路線，方法是直接從他們的瀏覽器流覽至。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="1a0b3-127">使用連結或調用，流覽至新的路由 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="1a0b3-128">如果消極式載入的元件包含可路由的元件，請將名為) 的[清單](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (例如，新增 `lazyLoadedAssemblies` 至元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="1a0b3-129">當元件包含可路由的元件時，元件會傳回至 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 集合。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="1a0b3-130">此架構會搜尋元件中的路由，並在找到任何新路由時更新路由集合。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="1a0b3-131">如果 `OnNavigateAsync` 回呼擲回未處理的例外狀況，則會叫用[ Blazor 錯誤 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="1a0b3-132">中的元件載入邏輯`OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="1a0b3-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="1a0b3-133">`OnNavigateAsync`具有 `NavigationContext` 參數，可提供目前非同步導覽事件的相關資訊，包括目標路徑 (`Path`) 和取消權杖 (`CancellationToken`) ：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="1a0b3-134">`Path`屬性是相對於應用程式基底路徑的使用者目的地路徑，例如 `/robot` 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="1a0b3-135">`CancellationToken`可以用來觀察非同步工作的取消作業。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="1a0b3-136">`OnNavigateAsync`當使用者流覽至不同的頁面時，自動取消目前正在執行的導覽工作。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="1a0b3-137">在中 `OnNavigateAsync` ，執行邏輯以決定要載入的元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="1a0b3-138">選項包括：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-138">Options include:</span></span>

* <span data-ttu-id="1a0b3-139">方法內的條件式檢查 `OnNavigateAsync` 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="1a0b3-140">對應至元件名稱之路由的查閱資料表，可以插入至元件或在區塊內執行 [`@code`](xref:mvc/views/razor#code) 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="1a0b3-141">`LazyAssemblyLoader`是一種架構提供的單一服務，可用於載入元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="1a0b3-142">插入 `LazyAssemblyLoader` `Router` 元件：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="1a0b3-143">`LazyAssemblyLoader`提供的 `LoadAssembliesAsync` 方法如下：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="1a0b3-144">使用 JS interop 透過網路呼叫來提取元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="1a0b3-145">將元件載入執行時間，在瀏覽器的 WebAssembly 上執行。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="1a0b3-146">架構的消極式載入實作為支援在伺服器上進行可呈現。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="1a0b3-147">在自動呈現期間，會假設載入所有元件，包括標示為消極式載入的元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="1a0b3-148">使用者與內容的互動 `<Navigating>`</span><span class="sxs-lookup"><span data-stu-id="1a0b3-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="1a0b3-149">載入元件時，這可能需要幾秒鐘的時間，因此 `Router` 元件可以向使用者指出頁面轉換已發生：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="1a0b3-150">加入命名空間的指示詞 [`@using`](xref:mvc/views/razor#using) <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="1a0b3-151">`<Navigating>`使用要在頁面轉換事件期間顯示的標記，將標記新增至元件。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="1a0b3-152">處理中的取消`OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="1a0b3-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="1a0b3-153">`NavigationContext`傳遞至回呼的物件 `OnNavigateAsync` 包含新的 `CancellationToken` 導覽事件發生時所設定的。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="1a0b3-154">`OnNavigateAsync`當此解除標記設定為避免繼續在過期的導覽上執行回呼時，回呼必須擲回 `OnNavigateAsync` 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="1a0b3-155">如果使用者流覽至路由 A，然後立即路由 B，則應用程式不應該繼續執行 `OnNavigateAsync` 路由 a 的回呼：</span><span class="sxs-lookup"><span data-stu-id="1a0b3-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="1a0b3-156">如果取消中的取消 token `NavigationContext` 可能會導致非預期的行為，例如從上一個導覽呈現元件，則不會擲回。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="1a0b3-157">完整範例</span><span class="sxs-lookup"><span data-stu-id="1a0b3-157">Complete example</span></span>

<span data-ttu-id="1a0b3-158">下列完整 `Router` 元件示範 `GrantImaharaRobotControls.dll` 當使用者導覽至時，載入元件 `/robot` 。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="1a0b3-159">在頁面轉換期間，使用者會看到樣式化的訊息。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="1a0b3-160">疑難排解</span><span class="sxs-lookup"><span data-stu-id="1a0b3-160">Troubleshoot</span></span>

* <span data-ttu-id="1a0b3-161">例如，如果發生未預期的轉譯 (例如，會) 呈現先前導覽中的元件，並確認如果已設定解除標記，程式碼就會擲回。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="1a0b3-162">如果元件仍在應用程式啟動時載入，請檢查元件是否已在專案檔中標示為延遲載入。</span><span class="sxs-lookup"><span data-stu-id="1a0b3-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a0b3-163">其他資源</span><span class="sxs-lookup"><span data-stu-id="1a0b3-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
