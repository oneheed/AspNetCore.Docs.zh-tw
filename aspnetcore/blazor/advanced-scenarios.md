---
title: ASP.NET Core 的 Blazor advanced 案例
author: guardrex
description: 瞭解中的先進案例 Blazor ，包括如何將手動 RenderTreeBuilder 邏輯併入應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: ba2bf91f3318225383ec9d164c34be9124aa311b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280849"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="ddc08-103">ASP.NET Core 的 Blazor advanced 案例</span><span class="sxs-lookup"><span data-stu-id="ddc08-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="ddc08-104">Blazor Server 電路處理常式</span><span class="sxs-lookup"><span data-stu-id="ddc08-104">Blazor Server circuit handler</span></span>

<span data-ttu-id="ddc08-105">Blazor Server 允許程式碼定義迴圈 *處理常式*，以允許在使用者的線路狀態變更時執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="ddc08-105">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="ddc08-106">在 `CircuitHandler` 應用程式的服務容器中衍生類別並將其註冊，會實作為電路處理常式。</span><span class="sxs-lookup"><span data-stu-id="ddc08-106">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="ddc08-107">下列的電路處理常式範例會追蹤開啟的 SignalR 連接：</span><span class="sxs-lookup"><span data-stu-id="ddc08-107">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="ddc08-108">線路處理常式是使用 DI 註冊的。</span><span class="sxs-lookup"><span data-stu-id="ddc08-108">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="ddc08-109">系統會為每個線路的實例建立範圍實例。</span><span class="sxs-lookup"><span data-stu-id="ddc08-109">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="ddc08-110">使用 `TrackingCircuitHandler` 上述範例中的，會建立單一服務，因為必須追蹤所有線路的狀態：</span><span class="sxs-lookup"><span data-stu-id="ddc08-110">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="ddc08-111">如果自訂電路處理常式的方法擲回未處理的例外狀況，則例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-111">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="ddc08-112">若要容忍處理常式程式碼或呼叫方法中的例外狀況，請將程式碼包裝在一或多個語句中， [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 並提供錯誤處理和記錄。</span><span class="sxs-lookup"><span data-stu-id="ddc08-112">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="ddc08-113">當線路因為使用者已中斷連線而結束，且架構正在清除電路狀態時，架構會處置電路的 DI 範圍。</span><span class="sxs-lookup"><span data-stu-id="ddc08-113">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="ddc08-114">處置範圍會處置任何執行的線路範圍 DI 服務 <xref:System.IDisposable?displayProperty=fullName> 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-114">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="ddc08-115">如果任何 DI 服務在處置期間擲回未處理的例外狀況，則架構會記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ddc08-115">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="ddc08-116">手動 RenderTreeBuilder 邏輯</span><span class="sxs-lookup"><span data-stu-id="ddc08-116">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="ddc08-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供操作元件和元素的方法，包括以 c # 程式碼手動建立元件。</span><span class="sxs-lookup"><span data-stu-id="ddc08-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="ddc08-118">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 來建立元件是一個 advanced 案例。</span><span class="sxs-lookup"><span data-stu-id="ddc08-118">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="ddc08-119">格式不正確的元件 (例如，未封閉的標記標記) 可能會導致未定義的行為。</span><span class="sxs-lookup"><span data-stu-id="ddc08-119">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="ddc08-120">請考慮下列 `PetDetails` 元件，它可以手動內建在另一個元件中：</span><span class="sxs-lookup"><span data-stu-id="ddc08-120">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="ddc08-121">在下列範例中，方法中的迴圈會 `CreateComponent` 產生三個 `PetDetails` 元件。</span><span class="sxs-lookup"><span data-stu-id="ddc08-121">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="ddc08-122">在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 具有序號的方法中，序號是源程式碼號。</span><span class="sxs-lookup"><span data-stu-id="ddc08-122">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="ddc08-123">Blazor差異演算法依賴不同行程式碼（而不是不同的呼叫調用）對應的序號。</span><span class="sxs-lookup"><span data-stu-id="ddc08-123">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="ddc08-124">使用方法建立元件時 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ，請將序號的引數硬式編碼。</span><span class="sxs-lookup"><span data-stu-id="ddc08-124">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="ddc08-125">**使用計算或計數器來產生序號可能會導致效能不佳。**</span><span class="sxs-lookup"><span data-stu-id="ddc08-125">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="ddc08-126">如需詳細資訊，請參閱序號與程式 [程式碼號相關，而不是執行順序](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) 一節。</span><span class="sxs-lookup"><span data-stu-id="ddc08-126">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="ddc08-127">`BuiltContent` 元件：</span><span class="sxs-lookup"><span data-stu-id="ddc08-127">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="ddc08-128">中的類型 <xref:Microsoft.AspNetCore.Components.RenderTree> 允許處理轉譯作業的 *結果* 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-128">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="ddc08-129">這些是架構執行的內部詳細資料 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-129">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="ddc08-130">這些類型應該視為不 *穩定* ，未來的版本可能會變更。</span><span class="sxs-lookup"><span data-stu-id="ddc08-130">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="ddc08-131">序號與程式程式碼號和非執行順序相關</span><span class="sxs-lookup"><span data-stu-id="ddc08-131">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="ddc08-132">Razor 元件檔 (`.razor`) 一律會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="ddc08-132">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="ddc08-133">相較于解讀程式碼，編譯是可能的優勢，因為編譯步驟可以用來插入可在執行時間改善應用程式效能的資訊。</span><span class="sxs-lookup"><span data-stu-id="ddc08-133">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="ddc08-134">這些改進的主要範例包含 *序號*。</span><span class="sxs-lookup"><span data-stu-id="ddc08-134">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="ddc08-135">序號會向執行時間指出輸出來自哪些不同和已排序的程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="ddc08-135">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="ddc08-136">執行時間會使用這項資訊，以線性時間產生有效率的樹狀結構差異，這遠比一般樹狀結構差異演算法一般可能更快。</span><span class="sxs-lookup"><span data-stu-id="ddc08-136">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="ddc08-137">請考慮下列 Razor 元件 (`.razor`) 檔：</span><span class="sxs-lookup"><span data-stu-id="ddc08-137">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="ddc08-138">上述程式碼會編譯成如下所示的內容：</span><span class="sxs-lookup"><span data-stu-id="ddc08-138">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="ddc08-139">第一次執行程式碼時，產生器 `someFlag` 會 `true` 收到：</span><span class="sxs-lookup"><span data-stu-id="ddc08-139">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="ddc08-140">順序</span><span class="sxs-lookup"><span data-stu-id="ddc08-140">Sequence</span></span> | <span data-ttu-id="ddc08-141">類型</span><span class="sxs-lookup"><span data-stu-id="ddc08-141">Type</span></span>      | <span data-ttu-id="ddc08-142">資料</span><span class="sxs-lookup"><span data-stu-id="ddc08-142">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="ddc08-143">0</span><span class="sxs-lookup"><span data-stu-id="ddc08-143">0</span></span>        | <span data-ttu-id="ddc08-144">Text node</span><span class="sxs-lookup"><span data-stu-id="ddc08-144">Text node</span></span> | <span data-ttu-id="ddc08-145">First</span><span class="sxs-lookup"><span data-stu-id="ddc08-145">First</span></span>  |
| <span data-ttu-id="ddc08-146">1</span><span class="sxs-lookup"><span data-stu-id="ddc08-146">1</span></span>        | <span data-ttu-id="ddc08-147">Text node</span><span class="sxs-lookup"><span data-stu-id="ddc08-147">Text node</span></span> | <span data-ttu-id="ddc08-148">Second</span><span class="sxs-lookup"><span data-stu-id="ddc08-148">Second</span></span> |

<span data-ttu-id="ddc08-149">想像這 `someFlag` 會變成 `false` ，然後再次轉譯標記。</span><span class="sxs-lookup"><span data-stu-id="ddc08-149">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="ddc08-150">這次，產生器會收到：</span><span class="sxs-lookup"><span data-stu-id="ddc08-150">This time, the builder receives:</span></span>

| <span data-ttu-id="ddc08-151">順序</span><span class="sxs-lookup"><span data-stu-id="ddc08-151">Sequence</span></span> | <span data-ttu-id="ddc08-152">類型</span><span class="sxs-lookup"><span data-stu-id="ddc08-152">Type</span></span>       | <span data-ttu-id="ddc08-153">資料</span><span class="sxs-lookup"><span data-stu-id="ddc08-153">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="ddc08-154">1</span><span class="sxs-lookup"><span data-stu-id="ddc08-154">1</span></span>        | <span data-ttu-id="ddc08-155">Text node</span><span class="sxs-lookup"><span data-stu-id="ddc08-155">Text node</span></span>  | <span data-ttu-id="ddc08-156">Second</span><span class="sxs-lookup"><span data-stu-id="ddc08-156">Second</span></span> |

<span data-ttu-id="ddc08-157">當執行時間執行差異時，會看到順序中的專案 `0` 已移除，因此它會產生下列簡單的 *編輯腳本*：</span><span class="sxs-lookup"><span data-stu-id="ddc08-157">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="ddc08-158">移除第一個文位元組點。</span><span class="sxs-lookup"><span data-stu-id="ddc08-158">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="ddc08-159">以程式設計方式產生序號的問題</span><span class="sxs-lookup"><span data-stu-id="ddc08-159">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="ddc08-160">想像一下，您應該改為撰寫下列轉譯樹產生器邏輯：</span><span class="sxs-lookup"><span data-stu-id="ddc08-160">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="ddc08-161">現在，第一個輸出是：</span><span class="sxs-lookup"><span data-stu-id="ddc08-161">Now, the first output is:</span></span>

| <span data-ttu-id="ddc08-162">順序</span><span class="sxs-lookup"><span data-stu-id="ddc08-162">Sequence</span></span> | <span data-ttu-id="ddc08-163">類型</span><span class="sxs-lookup"><span data-stu-id="ddc08-163">Type</span></span>      | <span data-ttu-id="ddc08-164">資料</span><span class="sxs-lookup"><span data-stu-id="ddc08-164">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="ddc08-165">0</span><span class="sxs-lookup"><span data-stu-id="ddc08-165">0</span></span>        | <span data-ttu-id="ddc08-166">Text node</span><span class="sxs-lookup"><span data-stu-id="ddc08-166">Text node</span></span> | <span data-ttu-id="ddc08-167">First</span><span class="sxs-lookup"><span data-stu-id="ddc08-167">First</span></span>  |
| <span data-ttu-id="ddc08-168">1</span><span class="sxs-lookup"><span data-stu-id="ddc08-168">1</span></span>        | <span data-ttu-id="ddc08-169">Text node</span><span class="sxs-lookup"><span data-stu-id="ddc08-169">Text node</span></span> | <span data-ttu-id="ddc08-170">Second</span><span class="sxs-lookup"><span data-stu-id="ddc08-170">Second</span></span> |

<span data-ttu-id="ddc08-171">此結果與先前的案例相同，因此不會有負面問題。</span><span class="sxs-lookup"><span data-stu-id="ddc08-171">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="ddc08-172">`someFlag` 是 `false` 在第二個轉譯上，輸出為：</span><span class="sxs-lookup"><span data-stu-id="ddc08-172">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="ddc08-173">順序</span><span class="sxs-lookup"><span data-stu-id="ddc08-173">Sequence</span></span> | <span data-ttu-id="ddc08-174">類型</span><span class="sxs-lookup"><span data-stu-id="ddc08-174">Type</span></span>      | <span data-ttu-id="ddc08-175">資料</span><span class="sxs-lookup"><span data-stu-id="ddc08-175">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="ddc08-176">0</span><span class="sxs-lookup"><span data-stu-id="ddc08-176">0</span></span>        | <span data-ttu-id="ddc08-177">Text node</span><span class="sxs-lookup"><span data-stu-id="ddc08-177">Text node</span></span> | <span data-ttu-id="ddc08-178">Second</span><span class="sxs-lookup"><span data-stu-id="ddc08-178">Second</span></span> |

<span data-ttu-id="ddc08-179">這次，diff 演算法會看到發生 *兩* 項變更，而演算法會產生下列編輯腳本：</span><span class="sxs-lookup"><span data-stu-id="ddc08-179">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="ddc08-180">將第一個文位元組點的值變更為 `Second` 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-180">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="ddc08-181">移除第二個文位元組點。</span><span class="sxs-lookup"><span data-stu-id="ddc08-181">Remove the second text node.</span></span>

<span data-ttu-id="ddc08-182">產生序號已遺失有關 `if/else` 原始程式碼中的分支和迴圈出現位置的所有實用資訊。</span><span class="sxs-lookup"><span data-stu-id="ddc08-182">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="ddc08-183">這會導致差異 **兩次（只要** 前面）。</span><span class="sxs-lookup"><span data-stu-id="ddc08-183">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="ddc08-184">這是一個簡單的範例。</span><span class="sxs-lookup"><span data-stu-id="ddc08-184">This is a trivial example.</span></span> <span data-ttu-id="ddc08-185">在更實際的案例中，如果是複雜且深層的嵌套結構，特別是迴圈，則效能成本通常較高。</span><span class="sxs-lookup"><span data-stu-id="ddc08-185">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="ddc08-186">差異演算法必須在轉譯樹狀結構中進行深度遞迴，而不是立即識別已插入或移除的迴圈區塊或分支。</span><span class="sxs-lookup"><span data-stu-id="ddc08-186">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="ddc08-187">這通常是因為 diff 演算法 misinformed 了舊的和新的結構彼此之間的關聯性，而導致必須建立較長的編輯腳本。</span><span class="sxs-lookup"><span data-stu-id="ddc08-187">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="ddc08-188">指引和結論</span><span class="sxs-lookup"><span data-stu-id="ddc08-188">Guidance and conclusions</span></span>

* <span data-ttu-id="ddc08-189">如果序號是動態產生的，應用程式效能就會受到影響。</span><span class="sxs-lookup"><span data-stu-id="ddc08-189">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="ddc08-190">架構無法在執行時間自動建立自己的序號，因為必要的資訊不存在，除非它是在編譯時期進行捕捉。</span><span class="sxs-lookup"><span data-stu-id="ddc08-190">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="ddc08-191">請勿撰寫很長的手動執行 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 邏輯區塊。</span><span class="sxs-lookup"><span data-stu-id="ddc08-191">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="ddc08-192">偏好使用檔案 `.razor` ，並讓編譯器處理序號。</span><span class="sxs-lookup"><span data-stu-id="ddc08-192">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="ddc08-193">如果您無法避免手動 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 邏輯，請將很長的程式碼區塊分割成包裝在呼叫中的較小部分 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-193">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="ddc08-194">每個區域都有自己的序號不同的空間，因此您可以從零 (或在每個區域內) 任何其他任意數目的任一數字重新開機。</span><span class="sxs-lookup"><span data-stu-id="ddc08-194">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="ddc08-195">如果序號是硬式編碼，則差異演算法只會要求序號增加值。</span><span class="sxs-lookup"><span data-stu-id="ddc08-195">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="ddc08-196">初始值與間距無關。</span><span class="sxs-lookup"><span data-stu-id="ddc08-196">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="ddc08-197">其中一個合法的選項是使用程式程式碼號作為序號，或從零開始，並依一個或數百個 (或任何偏好的間隔) 來增加。</span><span class="sxs-lookup"><span data-stu-id="ddc08-197">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="ddc08-198">Blazor 使用序號，其他樹狀結構比較的 UI 架構則不會使用它們。</span><span class="sxs-lookup"><span data-stu-id="ddc08-198">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="ddc08-199">使用序號時，比較會更快，且 Blazor 具有可自動處理開發人員撰寫檔案之序號的編譯步驟 `.razor` 。</span><span class="sxs-lookup"><span data-stu-id="ddc08-199">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
