---
title: 建立和使用 ASP.NET Core Razor 元件
author: guardrex
description: 瞭解如何建立和使用 Razor 元件，包括如何系結至資料、處理事件，以及管理元件生命週期。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/components/index
ms.openlocfilehash: cc4604f7f67a6648c96e099572ff27bfed838916
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981865"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="d0174-103">建立和使用 ASP.NET Core Razor 元件</span><span class="sxs-lookup"><span data-stu-id="d0174-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="d0174-104">[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="d0174-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="d0174-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d0174-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d0174-106">Blazor 應用程式是使用 *元件* 建立的。</span><span class="sxs-lookup"><span data-stu-id="d0174-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="d0174-107">元件是獨立的使用者介面區塊， (UI) ，例如頁面、對話方塊或表單。</span><span class="sxs-lookup"><span data-stu-id="d0174-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="d0174-108">元件包含 HTML 標籤以及插入資料或回應 UI 事件所需的處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="d0174-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="d0174-109">元件具有彈性和輕量。</span><span class="sxs-lookup"><span data-stu-id="d0174-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="d0174-110">這些專案可以在專案之間進行嵌套、重複使用及共用。</span><span class="sxs-lookup"><span data-stu-id="d0174-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="d0174-111">元件類別</span><span class="sxs-lookup"><span data-stu-id="d0174-111">Component classes</span></span>

<span data-ttu-id="d0174-112">元件會 [Razor](xref:mvc/views/razor) `.razor` 使用 c # 和 HTML 標籤的組合，在元件檔 () 中實作為元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="d0174-113">中的元件 Blazor 正式參考為 *Razor 元件*。</span><span class="sxs-lookup"><span data-stu-id="d0174-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="d0174-114">Razor 語法</span><span class="sxs-lookup"><span data-stu-id="d0174-114">Razor syntax</span></span>

<span data-ttu-id="d0174-115">Razor 應用程式中的元件會 Blazor 廣泛使用 Razor 語法。</span><span class="sxs-lookup"><span data-stu-id="d0174-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="d0174-116">如果您不熟悉 Razor 標記語言，建議您 <xref:mvc/views/razor> 在繼續之前先閱讀。</span><span class="sxs-lookup"><span data-stu-id="d0174-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="d0174-117">存取語法上的內容時 Razor ，請特別注意下列各節：</span><span class="sxs-lookup"><span data-stu-id="d0174-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="d0174-118">[Directives](xref:mvc/views/razor#directives) `@` 指示詞：前置詞保留關鍵字，通常會變更元件標記的剖析或運作方式。</span><span class="sxs-lookup"><span data-stu-id="d0174-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="d0174-119">[Directive attributes](xref:mvc/views/razor#directive-attributes) `@` 指示詞屬性：-前置詞，通常會變更元件元素剖析或運作的方式。</span><span class="sxs-lookup"><span data-stu-id="d0174-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="d0174-120">名稱</span><span class="sxs-lookup"><span data-stu-id="d0174-120">Names</span></span>

<span data-ttu-id="d0174-121">元件的名稱必須以大寫字元開頭。</span><span class="sxs-lookup"><span data-stu-id="d0174-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="d0174-122">例如， `MyCoolComponent.razor` 有效，且 `myCoolComponent.razor` 無效。</span><span class="sxs-lookup"><span data-stu-id="d0174-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="d0174-123">路由</span><span class="sxs-lookup"><span data-stu-id="d0174-123">Routing</span></span>

<span data-ttu-id="d0174-124">中的路由傳送 Blazor 可透過提供應用程式中每個可存取元件的路由範本來達成。</span><span class="sxs-lookup"><span data-stu-id="d0174-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="d0174-125">當編譯具有指示詞的檔案時 Razor [`@page`][9] ，產生的類別會 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由範本。</span><span class="sxs-lookup"><span data-stu-id="d0174-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="d0174-126">在執行時間，路由器會尋找具有的元件類別 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ，並轉譯任何元件具有符合所要求 URL 的路由範本。</span><span class="sxs-lookup"><span data-stu-id="d0174-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="d0174-127">如需詳細資訊，請參閱<xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="d0174-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="d0174-128">標記</span><span class="sxs-lookup"><span data-stu-id="d0174-128">Markup</span></span>

<span data-ttu-id="d0174-129">元件的 UI 是使用 HTML 來定義。</span><span class="sxs-lookup"><span data-stu-id="d0174-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="d0174-130">動態轉譯邏輯 (例如，迴圈、條件、運算式) 是使用稱為的內嵌 c # 語法來新增 *Razor* 。</span><span class="sxs-lookup"><span data-stu-id="d0174-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="d0174-131">當編譯應用程式時，會將 HTML 標籤和 c # 轉譯邏輯轉換成元件類別。</span><span class="sxs-lookup"><span data-stu-id="d0174-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="d0174-132">產生之類別的名稱符合檔案名。</span><span class="sxs-lookup"><span data-stu-id="d0174-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="d0174-133">元件類別的成員是在區塊中定義 [`@code`][1] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="d0174-134">在 [`@code`][1] 區塊中，[元件狀態] ([屬性]、[欄位]) 是以事件處理方法或定義其他元件邏輯的方法所指定。</span><span class="sxs-lookup"><span data-stu-id="d0174-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="d0174-135">允許一個以上 [`@code`][1] 的區塊。</span><span class="sxs-lookup"><span data-stu-id="d0174-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="d0174-136">元件成員可以使用以開頭的 c # 運算式作為元件轉譯邏輯的一部分 `@` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="d0174-137">例如，c # 欄位的呈現方式是在功能變數名稱前面加上 `@` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="d0174-138">下列範例會評估和轉譯：</span><span class="sxs-lookup"><span data-stu-id="d0174-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="d0174-139">`headingFontStyle` 至的 CSS 屬性值 `font-style` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="d0174-140">`headingText` 至元素的內容 `<h1>` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="d0174-141">在元件一開始呈現之後，元件會重新產生其轉譯樹狀結構，以回應事件。</span><span class="sxs-lookup"><span data-stu-id="d0174-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="d0174-142">Blazor 然後比較新的轉譯樹狀結構與上一個樹狀結構，並將任何修改套用至瀏覽器的檔物件模型 (DOM) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="d0174-143">元件是一般的 c # 類別，可放在專案內的任何位置。</span><span class="sxs-lookup"><span data-stu-id="d0174-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="d0174-144">產生網頁的元件通常位於資料夾中 `Pages` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="d0174-145">非頁面元件通常會放在資料夾中， `Shared` 或加入至專案的自訂資料夾中。</span><span class="sxs-lookup"><span data-stu-id="d0174-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="d0174-146">命名空間</span><span class="sxs-lookup"><span data-stu-id="d0174-146">Namespaces</span></span>

<span data-ttu-id="d0174-147">一般而言，元件的命名空間是從應用程式的根命名空間和元件在應用程式中)  (資料夾的位置衍生。</span><span class="sxs-lookup"><span data-stu-id="d0174-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="d0174-148">如果應用程式的根命名空間為 `BlazorSample` ，而且 `Counter` 元件位於 `Pages` 資料夾中：</span><span class="sxs-lookup"><span data-stu-id="d0174-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="d0174-149">`Counter`元件的命名空間為 `BlazorSample.Pages` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="d0174-150">元件的完整類型名稱為 `BlazorSample.Pages.Counter` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="d0174-151">若為保存元件的自訂資料夾，請將指示詞新增 [`@using`][2] 至父元件或應用程式的檔案 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="d0174-152">下列範例會讓資料夾中的元件 `Components` 可供使用：</span><span class="sxs-lookup"><span data-stu-id="d0174-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="d0174-153">元件也可以使用其完整名稱來參考，而不需要指示詞 [`@using`][2] ：</span><span class="sxs-lookup"><span data-stu-id="d0174-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="d0174-154">使用撰寫之元件的命名空間 Razor 是以優先權順序) 的 (為基礎：</span><span class="sxs-lookup"><span data-stu-id="d0174-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="d0174-155">[`@namespace`][8] 檔案 (中的指定 Razor `.razor`) 標記 (`@namespace BlazorSample.MyNamespace`) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="d0174-156">專案檔中的專案會 `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="d0174-157">專案名稱（取自專案檔的檔案名 (`.csproj`) ），以及從專案根目錄到元件的路徑。</span><span class="sxs-lookup"><span data-stu-id="d0174-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="d0174-158">例如，架構會將 `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) 解析為命名空間 `BlazorSample.Pages` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="d0174-159">元件遵循 c # 名稱系結規則。</span><span class="sxs-lookup"><span data-stu-id="d0174-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="d0174-160">針對 `Index` 此範例中的元件，範圍中的元件都是所有的元件：</span><span class="sxs-lookup"><span data-stu-id="d0174-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="d0174-161">在相同的資料夾中 `Pages` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="d0174-162">專案根目錄中未明確指定不同命名空間的元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="d0174-163">`global::`不支援資格。</span><span class="sxs-lookup"><span data-stu-id="d0174-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="d0174-164">使用別名語句匯入元件 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (例如， `@using Foo = Bar` 不支援) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="d0174-165">不支援部分限定名稱。</span><span class="sxs-lookup"><span data-stu-id="d0174-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="d0174-166">例如， `@using BlazorSample` 不支援新增和參考 `NavMenu` 元件 (`NavMenu.razor`) `<Shared.NavMenu></Shared.NavMenu>` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="d0174-167">部分類別支援</span><span class="sxs-lookup"><span data-stu-id="d0174-167">Partial class support</span></span>

<span data-ttu-id="d0174-168">Razor 元件會以部分類別的形式產生。</span><span class="sxs-lookup"><span data-stu-id="d0174-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="d0174-169">Razor 您可以使用下列其中一種方法來撰寫元件：</span><span class="sxs-lookup"><span data-stu-id="d0174-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="d0174-170">C # 程式碼是以 [`@code`][1] 單一檔案中的 HTML 標籤和程式碼區塊來定義 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d0174-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="d0174-171">Blazor 範本會 Razor 使用這種方法來定義其元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="d0174-172">C # 程式碼會放在定義為部分類別的程式碼後置檔案中。</span><span class="sxs-lookup"><span data-stu-id="d0174-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="d0174-173">下列範例顯示 `Counter` [`@code`][1] 從範本產生的應用程式中有區塊的預設元件 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="d0174-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="d0174-174">HTML 標籤、程式 Razor 代碼和 c # 程式碼位於相同的檔案中：</span><span class="sxs-lookup"><span data-stu-id="d0174-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="d0174-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="d0174-176">`Counter`元件也可以使用具有部分類別的程式碼後端檔案來建立：</span><span class="sxs-lookup"><span data-stu-id="d0174-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="d0174-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="d0174-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="d0174-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="d0174-179">視需要將任何必要的命名空間加入至部分類別檔案。</span><span class="sxs-lookup"><span data-stu-id="d0174-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="d0174-180">元件所使用的一般命名空間 Razor 包括：</span><span class="sxs-lookup"><span data-stu-id="d0174-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="d0174-181">[`@using`][2] 檔案中的指示詞 `_Imports.razor` 只會套用至檔案 Razor (`.razor`) ，而非 c # 檔案 (`.cs`) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="d0174-182">指定基類</span><span class="sxs-lookup"><span data-stu-id="d0174-182">Specify a base class</span></span>

<span data-ttu-id="d0174-183">指示詞 [`@inherits`][6] 可以用來指定元件的基類。</span><span class="sxs-lookup"><span data-stu-id="d0174-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="d0174-184">下列範例會顯示元件如何繼承基類， `BlazorRocksBase` 以提供元件的屬性和方法。</span><span class="sxs-lookup"><span data-stu-id="d0174-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="d0174-185">基類應衍生自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 。</span><span class="sxs-lookup"><span data-stu-id="d0174-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="d0174-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="d0174-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="d0174-187">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="d0174-188">使用元件</span><span class="sxs-lookup"><span data-stu-id="d0174-188">Use components</span></span>

<span data-ttu-id="d0174-189">元件可以使用 HTML 元素語法來宣告它們，以包含其他元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="d0174-190">使用元件的標記看起來像是 HTML 標籤，其中標籤名稱是元件類型。</span><span class="sxs-lookup"><span data-stu-id="d0174-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="d0174-191">中的下列標記會 `Pages/Index.razor` 呈現 `HeadingComponent` 實例：</span><span class="sxs-lookup"><span data-stu-id="d0174-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="d0174-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="d0174-193">如果元件包含的 HTML 元素具有不符合元件名稱的大寫第一個字母，則會發出警告，指出元素有未預期的名稱。</span><span class="sxs-lookup"><span data-stu-id="d0174-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="d0174-194">新增 [`@using`][2] 元件命名空間的指示詞會讓元件可供使用，以解決警告。</span><span class="sxs-lookup"><span data-stu-id="d0174-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="d0174-195">參數</span><span class="sxs-lookup"><span data-stu-id="d0174-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="d0174-196">路由參數</span><span class="sxs-lookup"><span data-stu-id="d0174-196">Route parameters</span></span>

<span data-ttu-id="d0174-197">元件可以從指示詞中提供的路由範本接收路由參數 [`@page`][9] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="d0174-198">路由器會使用路由參數來填入對應的元件參數。</span><span class="sxs-lookup"><span data-stu-id="d0174-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d0174-199">支援選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="d0174-199">Optional parameters are supported.</span></span> <span data-ttu-id="d0174-200">在下列範例中， `text` 選擇性參數會將路由區段的值指派給元件的 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d0174-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="d0174-201">如果區段不存在，的值 `Text` 會設定為 `fantastic` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="d0174-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d0174-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="d0174-204">不支援選擇性參數，因此 [`@page`][9] 在上述範例中會套用兩個指示詞。</span><span class="sxs-lookup"><span data-stu-id="d0174-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="d0174-205">第一個可讓您在不使用參數的情況下流覽至元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d0174-206">第二個指示詞會 [`@page`][9] 接收 `{text}` route 參數，並將值指派給 `Text` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d0174-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="d0174-207">如需捕捉所有路由參數的詳細資訊 (`{*pageRoute}`) ，以在多個資料夾界限之間取得路徑，請參閱 <xref:blazor/fundamentals/routing#catch-all-route-parameters> 。</span><span class="sxs-lookup"><span data-stu-id="d0174-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="d0174-208">元件參數</span><span class="sxs-lookup"><span data-stu-id="d0174-208">Component parameters</span></span>

<span data-ttu-id="d0174-209">元件可以有 *元件參數*，這些參數是使用元件類別上的公用簡單或複雜屬性（attribute）來定義的 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="d0174-210">使用這些屬性來指定標記中元件的引數。</span><span class="sxs-lookup"><span data-stu-id="d0174-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="d0174-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="d0174-212">在範例應用程式的下列範例中，會 `ParentComponent` 設定的 `Title` 屬性值 `ChildComponent` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-212">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="d0174-213">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-213">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="d0174-214">依照慣例，由 c # 程式碼所組成的屬性值會使用[ Razor 的保留 `@` 符號](xref:mvc/views/razor#razor-syntax)指派給參數：</span><span class="sxs-lookup"><span data-stu-id="d0174-214">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="d0174-215">父欄位或屬性： `Title="@{FIELD OR PROPERTY}` ，其中預留位置 `{FIELD OR PROPERTY}` 是父元件的 c # 欄位或屬性。</span><span class="sxs-lookup"><span data-stu-id="d0174-215">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="d0174-216">方法的結果： `Title="@{METHOD}"` ，其中預留位置 `{METHOD}` 是父元件的 c # 方法。</span><span class="sxs-lookup"><span data-stu-id="d0174-216">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="d0174-217">[隱含或明確運算式](xref:mvc/views/razor#implicit-razor-expressions)： `Title="@({EXPRESSION})"` ，其中預留位置 `{EXPRESSION}` 是 c # 運算式。</span><span class="sxs-lookup"><span data-stu-id="d0174-217">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="d0174-218">如需詳細資訊，請參閱<xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="d0174-218">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="d0174-219">請勿建立會寫入其本身 *元件參數* 的元件，而是改用私用欄位。</span><span class="sxs-lookup"><span data-stu-id="d0174-219">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="d0174-220">如需詳細資訊，請參閱 [覆寫的參數](#overwritten-parameters) 一節。</span><span class="sxs-lookup"><span data-stu-id="d0174-220">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="d0174-221">子內容</span><span class="sxs-lookup"><span data-stu-id="d0174-221">Child content</span></span>

<span data-ttu-id="d0174-222">元件可以設定另一個元件的內容。</span><span class="sxs-lookup"><span data-stu-id="d0174-222">Components can set the content of another component.</span></span> <span data-ttu-id="d0174-223">指派元件會在指定接收元件的標記之間提供內容。</span><span class="sxs-lookup"><span data-stu-id="d0174-223">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="d0174-224">在下列範例中， `ChildComponent` 有一個 `ChildContent` 代表的屬性 <xref:Microsoft.AspNetCore.Components.RenderFragment> ，代表要轉譯的 UI 區段。</span><span class="sxs-lookup"><span data-stu-id="d0174-224">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="d0174-225">的值位於 `ChildContent` 應呈現內容的元件標記中。</span><span class="sxs-lookup"><span data-stu-id="d0174-225">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="d0174-226">的值 `ChildContent` 是從父元件接收，並在啟動程式面板內轉譯 `panel-body` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-226">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="d0174-227">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-227">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="d0174-228">接收內容的屬性 <xref:Microsoft.AspNetCore.Components.RenderFragment> 必須依照慣例命名 `ChildContent` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-228">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="d0174-229">`ParentComponent`範例應用程式中的會將 `ChildComponent` 內容放在標記內，以提供轉譯的內容 `<ChildComponent>` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-229">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="d0174-230">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-230">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="d0174-231">由於轉譯 Blazor 子內容的方式， `for` 如果在子元件的內容中使用遞增迴圈變數，則在迴圈內轉譯元件需要本機索引變數：</span><span class="sxs-lookup"><span data-stu-id="d0174-231">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="d0174-232">或者，使用 `foreach` 迴圈搭配 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="d0174-232">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="d0174-233">屬性展開和任意參數</span><span class="sxs-lookup"><span data-stu-id="d0174-233">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="d0174-234">元件除了元件的宣告參數之外，還可以捕獲和轉譯其他屬性。</span><span class="sxs-lookup"><span data-stu-id="d0174-234">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="d0174-235">您可以在字典中捕捉其他屬性，然後在使用指示詞轉譯元件時， *splatted* 至元素 [`@attributes`][3] Razor 。</span><span class="sxs-lookup"><span data-stu-id="d0174-235">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="d0174-236">當您定義的元件會產生支援各種自訂專案的標記專案時，此案例很有用。</span><span class="sxs-lookup"><span data-stu-id="d0174-236">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="d0174-237">例如，針對 `<input>` 支援許多參數的來個別定義屬性可能相當繁瑣。</span><span class="sxs-lookup"><span data-stu-id="d0174-237">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="d0174-238">在下列範例中，第一個專案 `<input>` (`id="useIndividualParams"`) 使用個別的元件參數，而第二個 `<input>` 元素 (`id="useAttributesDict"`) 使用屬性展開：</span><span class="sxs-lookup"><span data-stu-id="d0174-238">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="d0174-239">參數的類型必須 `IEnumerable<KeyValuePair<string, object>>` `IReadOnlyDictionary<string, object>` 使用字串索引鍵來執行。</span><span class="sxs-lookup"><span data-stu-id="d0174-239">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="d0174-240">`<input>`使用這兩種方法的轉譯元素相同：</span><span class="sxs-lookup"><span data-stu-id="d0174-240">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="d0174-241">若要接受任意屬性，請使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 屬性設定為的屬性來定義元件參數 `true` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-241">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="d0174-242"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>上的屬性 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 可讓參數符合所有不符合任何其他參數的屬性。</span><span class="sxs-lookup"><span data-stu-id="d0174-242">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="d0174-243">元件只能用來定義單一參數 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 。</span><span class="sxs-lookup"><span data-stu-id="d0174-243">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="d0174-244">搭配使用的屬性類型， <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 必須可從 `Dictionary<string, object>` 使用字串索引鍵來指派。</span><span class="sxs-lookup"><span data-stu-id="d0174-244">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="d0174-245">`IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>` 也是此案例中的選項。</span><span class="sxs-lookup"><span data-stu-id="d0174-245">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="d0174-246">[`@attributes`][3]相對於元素屬性位置的位置是很重要的。</span><span class="sxs-lookup"><span data-stu-id="d0174-246">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="d0174-247">在專案 [`@attributes`][3] 上 splatted 時，會由右至左處理屬性 (最後一個) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-247">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="d0174-248">請考慮下列使用元件的元件範例 `Child` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-248">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="d0174-249">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-249">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="d0174-250">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-250">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="d0174-251">`Child`元件的 `extra` 屬性會設定為的右邊 [`@attributes`][3] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-251">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="d0174-252">`Parent` `<div>` `extra="5"` 由於屬性是由右至左 (最後一個) 來處理，因此，在透過其他屬性傳遞時，會包含該元件的所呈現內容。</span><span class="sxs-lookup"><span data-stu-id="d0174-252">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="d0174-253">在下列範例中，和的順序 `extra` [`@attributes`][3] 會在 `Child` 元件的中反轉 `<div>` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-253">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="d0174-254">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-254">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="d0174-255">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d0174-255">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="d0174-256">`<div>` `Parent` `extra="10"` 當透過其他屬性傳遞時，元件中所呈現的會包含：</span><span class="sxs-lookup"><span data-stu-id="d0174-256">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="d0174-257">捕獲元件的參考</span><span class="sxs-lookup"><span data-stu-id="d0174-257">Capture references to components</span></span>

<span data-ttu-id="d0174-258">元件參考提供一個參考元件實例的方式，讓您可以將命令發出至該實例，例如 `Show` 或 `Reset` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-258">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="d0174-259">若要捕獲元件參考：</span><span class="sxs-lookup"><span data-stu-id="d0174-259">To capture a component reference:</span></span>

* <span data-ttu-id="d0174-260">將 [`@ref`][4] 屬性新增至子元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-260">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="d0174-261">使用與子元件相同的類型來定義欄位。</span><span class="sxs-lookup"><span data-stu-id="d0174-261">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="d0174-262">轉譯元件時，此 `loginDialog` 欄位會填入 `MyLoginDialog` 子元件實例。</span><span class="sxs-lookup"><span data-stu-id="d0174-262">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="d0174-263">然後，您可以在元件實例上叫用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="d0174-263">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d0174-264">`loginDialog`變數只會在呈現元件之後填入，且其輸出會包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="d0174-264">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="d0174-265">在呈現元件之前，不需要參考任何專案。</span><span class="sxs-lookup"><span data-stu-id="d0174-265">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="d0174-266">若要在元件完成轉譯之後操作元件參考，請使用[ `OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="d0174-266">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="d0174-267">若要在事件處理常式中使用參考變數，請使用 lambda 運算式，或在[ `OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)中指派事件處理常式委派。</span><span class="sxs-lookup"><span data-stu-id="d0174-267">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="d0174-268">這可確保在指派事件處理常式之前，會先指派參考變數。</span><span class="sxs-lookup"><span data-stu-id="d0174-268">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="d0174-269">若要參考迴圈中的元件，請參閱 [ (dotnet/aspnetcore #13358) ，捕捉多個類似子元件的參考 ](https://github.com/dotnet/aspnetcore/issues/13358)。</span><span class="sxs-lookup"><span data-stu-id="d0174-269">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="d0174-270">雖然捕捉元件參考使用類似的語法來 [捕捉元素參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)，但它並不是 JavaScript interop 功能。</span><span class="sxs-lookup"><span data-stu-id="d0174-270">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="d0174-271">元件參考不會傳遞至 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="d0174-271">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="d0174-272">元件參考僅適用于 .NET 程式碼。</span><span class="sxs-lookup"><span data-stu-id="d0174-272">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="d0174-273">請勿 **使用元件** 參考來改變子元件的狀態。</span><span class="sxs-lookup"><span data-stu-id="d0174-273">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="d0174-274">相反地，請使用一般宣告式參數將資料傳遞給子元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-274">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="d0174-275">使用一般宣告式參數會導致子元件自動 rerender 正確的時間。</span><span class="sxs-lookup"><span data-stu-id="d0174-275">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="d0174-276">同步處理內容</span><span class="sxs-lookup"><span data-stu-id="d0174-276">Synchronization context</span></span>

<span data-ttu-id="d0174-277">Blazor 使用同步處理內容 (<xref:System.Threading.SynchronizationContext>) 來強制執行單一邏輯執行緒。</span><span class="sxs-lookup"><span data-stu-id="d0174-277">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="d0174-278">元件的 [生命週期方法](xref:blazor/components/lifecycle) 和所引發的任何事件回呼 Blazor 都會在同步處理內容上執行。</span><span class="sxs-lookup"><span data-stu-id="d0174-278">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="d0174-279">Blazor Server的同步處理內容會嘗試模擬單一執行緒環境，使它與瀏覽器中的 WebAssembly 模型緊密相符，也就是單一執行緒。</span><span class="sxs-lookup"><span data-stu-id="d0174-279">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="d0174-280">在任何給定的時間點，工作只會在一個執行緒上執行，以提供單一邏輯執行緒的印象。</span><span class="sxs-lookup"><span data-stu-id="d0174-280">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="d0174-281">不會同時執行兩個作業。</span><span class="sxs-lookup"><span data-stu-id="d0174-281">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="d0174-282">避免執行緒封鎖呼叫</span><span class="sxs-lookup"><span data-stu-id="d0174-282">Avoid thread-blocking calls</span></span>

<span data-ttu-id="d0174-283">一般而言，請勿呼叫下列方法。</span><span class="sxs-lookup"><span data-stu-id="d0174-283">Generally, don't call the following methods.</span></span> <span data-ttu-id="d0174-284">下列方法會封鎖執行緒，因此會封鎖應用程式繼續工作，直到基礎 <xref:System.Threading.Tasks.Task> 完成為止：</span><span class="sxs-lookup"><span data-stu-id="d0174-284">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="d0174-285">在外部叫用元件方法以更新狀態</span><span class="sxs-lookup"><span data-stu-id="d0174-285">Invoke component methods externally to update state</span></span>

<span data-ttu-id="d0174-286">在事件中，必須根據外來事件（例如計時器或其他通知）更新元件，然後使用 `InvokeAsync` 方法來分派至 Blazor 同步處理內容。</span><span class="sxs-lookup"><span data-stu-id="d0174-286">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="d0174-287">例如，假設有一個可通知任何已更新狀態之接聽元件的通知程式 *服務* ：</span><span class="sxs-lookup"><span data-stu-id="d0174-287">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="d0174-288">註冊 `NotifierService` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-288">Register the `NotifierService`:</span></span>

* <span data-ttu-id="d0174-289">在中 Blazor WebAssembly ，以 singleton 的形式註冊服務 `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-289">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="d0174-290">在中 Blazor Server ，註冊服務的範圍如下 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-290">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="d0174-291">使用 `NotifierService` 更新元件：</span><span class="sxs-lookup"><span data-stu-id="d0174-291">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="d0174-292">在上述範例中，會在的 `NotifierService` 同步處理內容之外叫用元件的 `OnNotify` 方法 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="d0174-292">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="d0174-293">`InvokeAsync` 用來切換至正確的內容，並將轉譯排入佇列。</span><span class="sxs-lookup"><span data-stu-id="d0174-293">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="d0174-294">使用 \@ 金鑰來控制元素和元件的保留</span><span class="sxs-lookup"><span data-stu-id="d0174-294">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="d0174-295">當轉譯元素或元件的清單，以及後續變更的元素或元件時， Blazor 的比較演算法必須決定可以保留先前的元素或元件，以及模型物件應該如何對應至這些專案或元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-295">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="d0174-296">一般來說，此程式是自動的，而且可以忽略，但在某些情況下，您可能會想要控制此進程。</span><span class="sxs-lookup"><span data-stu-id="d0174-296">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="d0174-297">請考慮下列範例：</span><span class="sxs-lookup"><span data-stu-id="d0174-297">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="d0174-298">集合的內容 `People` 可能會隨著插入、刪除或重新排序的專案而變更。</span><span class="sxs-lookup"><span data-stu-id="d0174-298">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="d0174-299">元件轉譯中時， `<DetailsEditor>` 元件可能會變更以接收不同的 `Details` 參數值。</span><span class="sxs-lookup"><span data-stu-id="d0174-299">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="d0174-300">這可能會導致比預期更複雜的轉譯資料流程。</span><span class="sxs-lookup"><span data-stu-id="d0174-300">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="d0174-301">在某些情況下，轉譯資料流程可能會導致可見的行為差異，例如遺失的元素焦點。</span><span class="sxs-lookup"><span data-stu-id="d0174-301">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="d0174-302">您可以使用指示詞屬性來控制對應進程 [`@key`][5] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-302">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="d0174-303">[`@key`][5] 使比較演算法保證根據索引鍵的值來保留元素或元件：</span><span class="sxs-lookup"><span data-stu-id="d0174-303">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="d0174-304">當 `People` 集合變更時，比較演算法會保留 `<DetailsEditor>` 實例和實例之間的關聯 `person` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-304">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="d0174-305">如果 `Person` 從清單中刪除 `People` ，則只 `<DetailsEditor>` 會從 UI 中移除對應的實例。</span><span class="sxs-lookup"><span data-stu-id="d0174-305">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="d0174-306">其他實例則保持不變。</span><span class="sxs-lookup"><span data-stu-id="d0174-306">Other instances are left unchanged.</span></span>
* <span data-ttu-id="d0174-307">如果 `Person` 插入清單中的某個位置，就 `<DetailsEditor>` 會在該對應位置插入一個新的實例。</span><span class="sxs-lookup"><span data-stu-id="d0174-307">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="d0174-308">其他實例則保持不變。</span><span class="sxs-lookup"><span data-stu-id="d0174-308">Other instances are left unchanged.</span></span>
* <span data-ttu-id="d0174-309">如果 `Person` 重新排序專案，則 `<DetailsEditor>` 會保留對應的實例，並在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="d0174-309">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="d0174-310">在某些情況下，使用可將 [`@key`][5] 轉譯資料流程的複雜性降至最低，並避免變更 DOM 的可設定狀態部分（例如焦點位置）。</span><span class="sxs-lookup"><span data-stu-id="d0174-310">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d0174-311">索引鍵是每個容器元素或元件的本機密鑰。</span><span class="sxs-lookup"><span data-stu-id="d0174-311">Keys are local to each container element or component.</span></span> <span data-ttu-id="d0174-312">索引鍵不會在檔之間進行全域比較。</span><span class="sxs-lookup"><span data-stu-id="d0174-312">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="d0174-313">使用金鑰的時機 \@</span><span class="sxs-lookup"><span data-stu-id="d0174-313">When to use \@key</span></span>

<span data-ttu-id="d0174-314">一般來說，在轉譯清單時使用 [`@key`][5] (例如，在 [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 區塊) 中，以及定義的適當值 [`@key`][5] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-314">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="d0174-315">當物件變更時，您也可以使用 [`@key`][5] 防止 Blazor 保留元素或元件子樹：</span><span class="sxs-lookup"><span data-stu-id="d0174-315">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="d0174-316">如果 `@currentPerson` 有變更，屬性指示詞會 [`@key`][5] 強制 Blazor 捨棄整個 `<div>` 和其子系，並使用新的元素和元件重建 UI 內的子樹。</span><span class="sxs-lookup"><span data-stu-id="d0174-316">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="d0174-317">如果您需要保證在變更時不會保留任何 UI 狀態，這會很有用 `@currentPerson` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-317">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="d0174-318">何時不使用 \@ 金鑰</span><span class="sxs-lookup"><span data-stu-id="d0174-318">When not to use \@key</span></span>

<span data-ttu-id="d0174-319">與進行比較時，會產生效能成本 [`@key`][5] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-319">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="d0174-320">效能成本不大，但只會指定 [`@key`][5] 控制元素或元件保留規則是否能讓應用程式受益。</span><span class="sxs-lookup"><span data-stu-id="d0174-320">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="d0174-321">即使 [`@key`][5] 未使用，也會盡可能 Blazor 保留子項目和元件實例。</span><span class="sxs-lookup"><span data-stu-id="d0174-321">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="d0174-322">使用的唯一優點 [`@key`][5] 是控制模型實例 *如何* 對應至保留的元件實例，而不是選取對應的比較演算法。</span><span class="sxs-lookup"><span data-stu-id="d0174-322">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="d0174-323">用於索引鍵的值 \@</span><span class="sxs-lookup"><span data-stu-id="d0174-323">What values to use for \@key</span></span>

<span data-ttu-id="d0174-324">一般來說，提供下列其中一種值的意義 [`@key`][5] ：</span><span class="sxs-lookup"><span data-stu-id="d0174-324">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="d0174-325">例如， `Person` 如先前範例所示的 (模型物件實例) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-325">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="d0174-326">這可確保根據物件參考相等來保留。</span><span class="sxs-lookup"><span data-stu-id="d0174-326">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="d0174-327">唯一識別碼 (例如，、或) 類型的主 `int` 鍵值 `string` `Guid` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-327">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="d0174-328">確定用於的值 [`@key`][5] 不會衝突。</span><span class="sxs-lookup"><span data-stu-id="d0174-328">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="d0174-329">如果在相同的父元素內偵測到衝突值，則會擲回例外狀況， Blazor 因為它無法將舊的元素或元件以決定性的方式對應至新的元素或元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-329">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="d0174-330">只使用相異的值，例如物件實例或主鍵值。</span><span class="sxs-lookup"><span data-stu-id="d0174-330">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="d0174-331">覆寫的參數</span><span class="sxs-lookup"><span data-stu-id="d0174-331">Overwritten parameters</span></span>

<span data-ttu-id="d0174-332">Blazor架構通常會施加安全的父系對子參數指派：</span><span class="sxs-lookup"><span data-stu-id="d0174-332">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="d0174-333">未預期地覆寫參數。</span><span class="sxs-lookup"><span data-stu-id="d0174-333">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="d0174-334">最小化副作用。</span><span class="sxs-lookup"><span data-stu-id="d0174-334">Side-effects are minimized.</span></span> <span data-ttu-id="d0174-335">例如，會避免其他轉譯，因為它們可能會建立無限的轉譯迴圈。</span><span class="sxs-lookup"><span data-stu-id="d0174-335">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="d0174-336">當父元件轉譯中時，子元件會收到可能覆寫現有值的新參數值。</span><span class="sxs-lookup"><span data-stu-id="d0174-336">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="d0174-337">在使用一或多個資料系結參數開發元件時，通常會發生不小心覆寫子元件中的參數值，而開發人員則會直接寫入子系中的參數：</span><span class="sxs-lookup"><span data-stu-id="d0174-337">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="d0174-338">子元件會以父元件中的一或多個參數值來呈現。</span><span class="sxs-lookup"><span data-stu-id="d0174-338">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="d0174-339">子系直接寫入參數的值。</span><span class="sxs-lookup"><span data-stu-id="d0174-339">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="d0174-340">父元件會轉譯中並覆寫子系參數的值。</span><span class="sxs-lookup"><span data-stu-id="d0174-340">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="d0174-341">覆寫辨識值的可能也會延伸至子元件的屬性 setter。</span><span class="sxs-lookup"><span data-stu-id="d0174-341">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="d0174-342">**我們的一般指引是不要建立直接寫入其本身參數的元件。**</span><span class="sxs-lookup"><span data-stu-id="d0174-342">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="d0174-343">請考慮下列有問題的 `Expander` 元件：</span><span class="sxs-lookup"><span data-stu-id="d0174-343">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="d0174-344">轉譯子內容。</span><span class="sxs-lookup"><span data-stu-id="d0174-344">Renders child content.</span></span>
* <span data-ttu-id="d0174-345">切換 () ，以元件參數顯示子內容 `Expanded` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-345">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="d0174-346">元件會直接寫入 `Expanded` 參數，以示範覆寫參數的問題，應該予以避免。</span><span class="sxs-lookup"><span data-stu-id="d0174-346">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="d0174-347">`Expander`元件會新增至可能呼叫的父元件 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ：</span><span class="sxs-lookup"><span data-stu-id="d0174-347">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="d0174-348">一開始， `Expander` 當元件的屬性切換時，元件會獨立運作 `Expanded` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-348">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="d0174-349">子元件會如預期般維護其狀態。</span><span class="sxs-lookup"><span data-stu-id="d0174-349">The child components maintain their states as expected.</span></span> <span data-ttu-id="d0174-350">當 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在父系中呼叫時， `Expanded` 第一個子元件的參數會重設回其初始值 (`true`) 。</span><span class="sxs-lookup"><span data-stu-id="d0174-350">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="d0174-351">第二個 `Expander` 元件的 `Expanded` 值不會重設，因為不會在第二個元件中轉譯任何子內容。</span><span class="sxs-lookup"><span data-stu-id="d0174-351">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="d0174-352">若要在先前的案例中維持狀態，請使用元件中的 *私用欄位* `Expander` 來維持其切換狀態。</span><span class="sxs-lookup"><span data-stu-id="d0174-352">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="d0174-353">下列修訂的 `Expander` 元件：</span><span class="sxs-lookup"><span data-stu-id="d0174-353">The following revised `Expander` component:</span></span>

* <span data-ttu-id="d0174-354">接受 `Expanded` 來自父系的元件參數值。</span><span class="sxs-lookup"><span data-stu-id="d0174-354">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="d0174-355">將元件參數值指派給 *private field* `expanded` [OnInitialized 事件](xref:blazor/components/lifecycle#component-initialization-methods)中 () 的私用欄位。</span><span class="sxs-lookup"><span data-stu-id="d0174-355">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="d0174-356">使用私用欄位來維護其內部切換狀態，以示範如何避免直接寫入參數。</span><span class="sxs-lookup"><span data-stu-id="d0174-356">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

<span data-ttu-id="d0174-357">如需詳細資訊，請參閱[ Blazor (dotnet/aspnetcore #24599) 的雙向](https://github.com/dotnet/aspnetcore/issues/24599)系結錯誤。</span><span class="sxs-lookup"><span data-stu-id="d0174-357">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="d0174-358">套用屬性</span><span class="sxs-lookup"><span data-stu-id="d0174-358">Apply an attribute</span></span>

<span data-ttu-id="d0174-359">您可以使用指示詞將屬性套用至 Razor 元件 [`@attribute`][7] 。</span><span class="sxs-lookup"><span data-stu-id="d0174-359">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="d0174-360">下列範例會將 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性套用至元件類別：</span><span class="sxs-lookup"><span data-stu-id="d0174-360">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="d0174-361">條件式 HTML 元素屬性</span><span class="sxs-lookup"><span data-stu-id="d0174-361">Conditional HTML element attributes</span></span>

<span data-ttu-id="d0174-362">HTML 元素屬性是根據 .NET 值以有條件的形式呈現。</span><span class="sxs-lookup"><span data-stu-id="d0174-362">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="d0174-363">如果值為 `false` 或 `null` ，則不會呈現屬性。</span><span class="sxs-lookup"><span data-stu-id="d0174-363">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="d0174-364">如果值為 `true` ，則會將屬性轉譯為最小化。</span><span class="sxs-lookup"><span data-stu-id="d0174-364">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="d0174-365">在下列範例中， `IsCompleted` 判斷 `checked` 是否在專案的標記中轉譯：</span><span class="sxs-lookup"><span data-stu-id="d0174-365">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="d0174-366">如果 `IsCompleted` 為 `true` ，則會將此核取方塊轉譯為：</span><span class="sxs-lookup"><span data-stu-id="d0174-366">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="d0174-367">如果 `IsCompleted` 為 `false` ，則會將此核取方塊轉譯為：</span><span class="sxs-lookup"><span data-stu-id="d0174-367">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="d0174-368">如需詳細資訊，請參閱<xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="d0174-368">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="d0174-369">當 .NET 類型為時，某些 HTML 屬性（例如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) ）無法正常運作 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-369">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="d0174-370">在這些情況下，請使用型別， `string` 而不是 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-370">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="d0174-371">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="d0174-371">Raw HTML</span></span>

<span data-ttu-id="d0174-372">字串通常會使用 DOM 文位元組點來呈現，這表示它們所包含的標記會被忽略，並視為常值文字。</span><span class="sxs-lookup"><span data-stu-id="d0174-372">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="d0174-373">若要轉譯原始 HTML，請將 HTML 內容包裝成 `MarkupString` 值。</span><span class="sxs-lookup"><span data-stu-id="d0174-373">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="d0174-374">值會剖析為 HTML 或 SVG 並插入 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="d0174-374">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="d0174-375">轉譯從任何未受信任來源所建立的原始 HTML 會有 **安全性風險** ，應予以避免！</span><span class="sxs-lookup"><span data-stu-id="d0174-375">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="d0174-376">下列範例顯示如何使用 `MarkupString` 型別，將靜態 HTML 內容的區塊加入至元件的轉譯輸出：</span><span class="sxs-lookup"><span data-stu-id="d0174-376">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="d0174-377">Razor 範本</span><span class="sxs-lookup"><span data-stu-id="d0174-377">Razor templates</span></span>

<span data-ttu-id="d0174-378">您可以使用範本語法來定義轉譯片段 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d0174-378">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="d0174-379">Razor 範本是定義 UI 程式碼片段的方式，並採用下列格式：</span><span class="sxs-lookup"><span data-stu-id="d0174-379">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="d0174-380">下列範例說明如何 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 在元件中直接指定和值，以及轉譯範本。</span><span class="sxs-lookup"><span data-stu-id="d0174-380">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="d0174-381">轉譯片段也可以做為引數傳遞至樣板 [化元件](xref:blazor/components/templated-components)。</span><span class="sxs-lookup"><span data-stu-id="d0174-381">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="d0174-382">上述程式碼的呈現輸出：</span><span class="sxs-lookup"><span data-stu-id="d0174-382">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="d0174-383">靜態資產</span><span class="sxs-lookup"><span data-stu-id="d0174-383">Static assets</span></span>

<span data-ttu-id="d0174-384">Blazor遵循 ASP.NET Core 應用程式將靜態資產放置於專案[ `web root (wwwroot)` 資料夾](xref:fundamentals/index#web-root)底下的慣例。</span><span class="sxs-lookup"><span data-stu-id="d0174-384">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="d0174-385">使用基底相對路徑 (`/`) 參考靜態資產的 web 根目錄。</span><span class="sxs-lookup"><span data-stu-id="d0174-385">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="d0174-386">在下列範例中， `logo.png` 實際上是位於資料夾中 `{PROJECT ROOT}/wwwroot/images` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-386">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="d0174-387">Razor 元件 () **不** 支援波狀符號斜線標記法 `~/` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-387">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="d0174-388">如需設定應用程式基底路徑的詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。</span><span class="sxs-lookup"><span data-stu-id="d0174-388">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="d0174-389">元件中不支援標記協助程式</span><span class="sxs-lookup"><span data-stu-id="d0174-389">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="d0174-390">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro))  (的元件中不支援 Razor `.razor` 。</span><span class="sxs-lookup"><span data-stu-id="d0174-390">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="d0174-391">若要在中提供標籤協助程式的功能，請使用與標籤協助程式 Blazor 相同的功能建立元件，並改為使用該元件。</span><span class="sxs-lookup"><span data-stu-id="d0174-391">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="d0174-392">可擴充的向量圖形 (SVG) 影像</span><span class="sxs-lookup"><span data-stu-id="d0174-392">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="d0174-393">由於 Blazor 呈現 HTML、瀏覽器支援的影像，包括可擴充的向量圖形 (SVG) 影像 (`.svg`) ）可透過 `<img>` 標記支援：</span><span class="sxs-lookup"><span data-stu-id="d0174-393">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="d0174-394">同樣地， () 的樣式表單檔案的 CSS 規則中支援 SVG 影像 `.css` ：</span><span class="sxs-lookup"><span data-stu-id="d0174-394">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="d0174-395">不過，並非所有案例都支援內嵌 SVG 標記。</span><span class="sxs-lookup"><span data-stu-id="d0174-395">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="d0174-396">如果您將 `<svg>` 標記直接放入元件檔 (`.razor`) ，則支援基本映射轉譯，但目前尚不支援許多先進的案例。</span><span class="sxs-lookup"><span data-stu-id="d0174-396">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="d0174-397">例如， `<use>` 目前未遵守標記，且 [`@bind`][10] 無法與某些 SVG 標記一起使用。</span><span class="sxs-lookup"><span data-stu-id="d0174-397">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="d0174-398">如需詳細資訊，請參閱 [ Blazor (dotnet/aspnetcore 中的 SVG 支援 #18271) ](https://github.com/dotnet/aspnetcore/issues/18271)。</span><span class="sxs-lookup"><span data-stu-id="d0174-398">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0174-399">其他資源</span><span class="sxs-lookup"><span data-stu-id="d0174-399">Additional resources</span></span>

* <span data-ttu-id="d0174-400"><xref:blazor/security/server/threat-mitigation>：包含建立 Blazor Server 必須與資源耗盡相關之應用程式的指引。</span><span class="sxs-lookup"><span data-stu-id="d0174-400"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
