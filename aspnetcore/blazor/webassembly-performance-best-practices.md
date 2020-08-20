---
title: ASP.NET Core Blazor WebAssembly 效能最佳做法
author: pranavkm
description: 提升 ASP.NET Core Blazor WebAssembly 應用程式效能，並避免常見效能問題的秘訣。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 819947be90e7f09c7ba853df1af1f3c7066c0219
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625813"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="b8924-103">ASP.NET Core Blazor WebAssembly 效能最佳做法</span><span class="sxs-lookup"><span data-stu-id="b8924-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="b8924-104">依 [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="b8924-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="b8924-105">本文提供 ASP.NET Core Blazor WebAssembly 效能最佳做法的指導方針。</span><span class="sxs-lookup"><span data-stu-id="b8924-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="b8924-106">避免不必要的元件呈現</span><span class="sxs-lookup"><span data-stu-id="b8924-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="b8924-107">Blazor的比較演算法可避免在演算法察覺元件未變更時轉譯資料流程元件。</span><span class="sxs-lookup"><span data-stu-id="b8924-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="b8924-108">覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> 以精細控制元件轉譯。</span><span class="sxs-lookup"><span data-stu-id="b8924-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="b8924-109">如果撰寫的僅限 UI 元件永遠不會在初始轉譯之後變更，請將設定 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 為傳回 `false` ：</span><span class="sxs-lookup"><span data-stu-id="b8924-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="b8924-110">大部分的應用程式都不需要更細緻的控制，但 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 可以用來選擇性地呈現回應 UI 事件的元件。</span><span class="sxs-lookup"><span data-stu-id="b8924-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="b8924-111"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>在呈現大量元件的案例中，使用可能也很重要。</span><span class="sxs-lookup"><span data-stu-id="b8924-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="b8924-112">請考慮方格，其中在方格 <xref:Microsoft.AspNetCore.Components.EventCallback> 上方格呼叫的一個儲存格中使用。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A></span><span class="sxs-lookup"><span data-stu-id="b8924-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="b8924-113">呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會導致每個子元件的重新呈現。</span><span class="sxs-lookup"><span data-stu-id="b8924-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="b8924-114">如果只有少量的資料格需要轉譯資料流程，請使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 來避免不必要轉譯的效能負面影響。</span><span class="sxs-lookup"><span data-stu-id="b8924-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="b8924-115">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b8924-115">In the following example:</span></span>

* <span data-ttu-id="b8924-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 會被覆寫並設定為欄位的值 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> ，這是一開始 `false` 載入元件的時間。</span><span class="sxs-lookup"><span data-stu-id="b8924-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="b8924-117">選取此按鈕時， <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 會將設定為 `true` ，以強制元件 rerender 更新的 `currentCount` 。</span><span class="sxs-lookup"><span data-stu-id="b8924-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="b8924-118">在轉譯資料流程之後，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 將的值設定為， <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` 以避免在下一次選取按鈕之前，進行進一步的轉譯資料流程。</span><span class="sxs-lookup"><span data-stu-id="b8924-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="b8924-119">如需詳細資訊，請參閱<xref:blazor/components/lifecycle#after-component-render>。</span><span class="sxs-lookup"><span data-stu-id="b8924-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="b8924-120">虛擬化可重複使用的片段</span><span class="sxs-lookup"><span data-stu-id="b8924-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="b8924-121">元件提供便利的方法來產生重複使用的程式碼和標記片段。</span><span class="sxs-lookup"><span data-stu-id="b8924-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="b8924-122">一般而言，我們建議您撰寫與應用程式需求最一致的個別元件。</span><span class="sxs-lookup"><span data-stu-id="b8924-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="b8924-123">有一點要注意的是，每個額外的子元件都有提供轉譯父元件所花費的總時間。</span><span class="sxs-lookup"><span data-stu-id="b8924-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="b8924-124">大部分的應用程式都可以忽略額外的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="b8924-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="b8924-125">產生大量元件的應用程式應該考慮使用策略來降低處理的額外負荷，例如限制轉譯的元件數目。</span><span class="sxs-lookup"><span data-stu-id="b8924-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="b8924-126">例如，呈現數百個包含元件之資料列的方格或清單，需要處理器密集的轉譯。</span><span class="sxs-lookup"><span data-stu-id="b8924-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="b8924-127">請考慮將方格或清單版面配置虛擬化，如此一來，就只會在任何指定時間轉譯元件的子集。</span><span class="sxs-lookup"><span data-stu-id="b8924-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="b8924-128">如需元件子集轉譯的範例，請參閱[ `Virtualization` (aspnet/samples GitHub 存放庫) 範例應用程式](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)中的下列元件：</span><span class="sxs-lookup"><span data-stu-id="b8924-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="b8924-129">`Virtualize` 元件 ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)) ：以 c # 撰寫的元件，它會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 根據使用者的滾動方式來呈現一組氣象資料列。</span><span class="sxs-lookup"><span data-stu-id="b8924-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="b8924-130">`FetchData` 元件 ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)) ：使用 `Virtualize` 元件一次顯示25個數據列的氣象資料。</span><span class="sxs-lookup"><span data-stu-id="b8924-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="b8924-131">避免 JavaScript interop 將資料封送處理</span><span class="sxs-lookup"><span data-stu-id="b8924-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="b8924-132">在中 Blazor WebAssembly ，JavaScript (JS) interop 呼叫必須跨越 WEBASSEMBLY js 界限。</span><span class="sxs-lookup"><span data-stu-id="b8924-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="b8924-133">在兩個內容中序列化和還原序列化內容會建立應用程式的處理負荷。</span><span class="sxs-lookup"><span data-stu-id="b8924-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="b8924-134">頻繁的 JS interop 呼叫通常會對效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="b8924-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="b8924-135">若要減少跨界限的資料封送處理，請判斷應用程式是否可以將許多小型承載合併成單一大型承載，以避免在 WebAssembly 和 JS 之間進行大量的內容切換。</span><span class="sxs-lookup"><span data-stu-id="b8924-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="b8924-136">使用 System.Text.Js于</span><span class="sxs-lookup"><span data-stu-id="b8924-136">Use System.Text.Json</span></span>

<span data-ttu-id="b8924-137">Blazor的 JS interop 實行相依于 <xref:System.Text.Json> ，這是具有低記憶體配置的高效能 JSON 序列化程式庫。</span><span class="sxs-lookup"><span data-stu-id="b8924-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="b8924-138">使用 <xref:System.Text.Json> 不會導致額外的應用程式承載大小超過新增一或多個替代的 JSON 程式庫。</span><span class="sxs-lookup"><span data-stu-id="b8924-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="b8924-139">如需遷移指引，請參閱[如何從 `Newtonsoft.Json` 遷移 `System.Text.Json` 至](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。</span><span class="sxs-lookup"><span data-stu-id="b8924-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="b8924-140">在適當的情況下使用同步和 unmarshalled JS interop Api</span><span class="sxs-lookup"><span data-stu-id="b8924-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="b8924-141">Blazor WebAssembly<xref:Microsoft.JSInterop.IJSRuntime>針對應用程式可用的單一版本提供兩個額外版本 Blazor Server ：</span><span class="sxs-lookup"><span data-stu-id="b8924-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="b8924-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> 允許同步叫用 JS interop 呼叫，其額外負荷比非同步版本更少：</span><span class="sxs-lookup"><span data-stu-id="b8924-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="b8924-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 允許 unmarshalled JS interop 呼叫：</span><span class="sxs-lookup"><span data-stu-id="b8924-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="b8924-144">雖然使用 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 具有最少的 JS interop 方法額外負荷，但與這些 api 互動所需的 JavaScript api 目前尚未記載，並且在未來版本中可能會有重大變更。</span><span class="sxs-lookup"><span data-stu-id="b8924-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="b8924-145">減少應用程式大小</span><span class="sxs-lookup"><span data-stu-id="b8924-145">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="b8924-146"> (IL) 連結的中繼語言</span><span class="sxs-lookup"><span data-stu-id="b8924-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="b8924-147">[連結 Blazor WebAssembly 應用程式](xref:blazor/host-and-deploy/configure-linker) 會藉由在應用程式的二進位檔中修剪未使用的程式碼，來減少應用程式的大小。</span><span class="sxs-lookup"><span data-stu-id="b8924-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="b8924-148">根據預設，連結器只會在建立設定時啟用 `Release` 。</span><span class="sxs-lookup"><span data-stu-id="b8924-148">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="b8924-149">若要從中獲益，請使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令並將 [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) 選項設為，以發行應用程式進行部署 `Release` ：</span><span class="sxs-lookup"><span data-stu-id="b8924-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="b8924-150">延遲載入元件</span><span class="sxs-lookup"><span data-stu-id="b8924-150">Lazy load assemblies</span></span>

<span data-ttu-id="b8924-151">當路由需要元件時，于執行時間載入元件。</span><span class="sxs-lookup"><span data-stu-id="b8924-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="b8924-152">如需詳細資訊，請參閱<xref:blazor/webassembly-lazy-load-assemblies>。</span><span class="sxs-lookup"><span data-stu-id="b8924-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="b8924-153">壓縮</span><span class="sxs-lookup"><span data-stu-id="b8924-153">Compression</span></span>

<span data-ttu-id="b8924-154">Blazor WebAssembly發佈應用程式時，會在發行期間以靜態方式壓縮輸出，以減少應用程式的大小，並移除執行時間壓縮的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="b8924-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="b8924-155">Blazor 依賴伺服器來執行內容 negotation，並提供靜態壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="b8924-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="b8924-156">部署應用程式之後，請確認應用程式會提供壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="b8924-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="b8924-157">檢查瀏覽器開發人員工具中的 [網路] 索引標籤，並確認檔案是由或提供服務 `Content-Encoding: br` `Content-Encoding: gz` 。</span><span class="sxs-lookup"><span data-stu-id="b8924-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="b8924-158">如果主機未提供壓縮檔案，請依照中的指示進行 <xref:blazor/host-and-deploy/webassembly#compression> 。</span><span class="sxs-lookup"><span data-stu-id="b8924-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="b8924-159">停用未使用的功能</span><span class="sxs-lookup"><span data-stu-id="b8924-159">Disable unused features</span></span>

<span data-ttu-id="b8924-160">Blazor WebAssembly的執行時間包含下列 .NET 功能，如果應用程式不需要它們來取得較小的承載大小，則可以停用這些功能：</span><span class="sxs-lookup"><span data-stu-id="b8924-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="b8924-161">包含資料檔案，以使時區資訊正確無誤。</span><span class="sxs-lookup"><span data-stu-id="b8924-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="b8924-162">如果應用程式不需要這項功能，請考慮將 `BlazorEnableTimeZoneSupport` 應用程式專案檔中的 MSBuild 屬性設為，以停用它 `false` ：</span><span class="sxs-lookup"><span data-stu-id="b8924-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="b8924-163">包含了定序資訊，以使 Api <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 正常運作。</span><span class="sxs-lookup"><span data-stu-id="b8924-163">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="b8924-164">如果您確定應用程式不需要定序資料，請考慮將 `BlazorWebAssemblyPreserveCollationData` 應用程式專案檔中的 MSBuild 屬性設為，以停用它 `false` ：</span><span class="sxs-lookup"><span data-stu-id="b8924-164">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
