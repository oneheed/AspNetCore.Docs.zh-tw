---
title: ASP.NET Core 的 Blazor advanced 案例
author: guardrex
description: 瞭解中的先進案例 Blazor ，包括如何將手動 RenderTreeBuilder 邏輯併入應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
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
ms.openlocfilehash: bbefdf7272cc09d09baa2e5f2a42d04f91eca1bd
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711200"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="ba273-103">ASP.NET Core 的 Blazor advanced 案例</span><span class="sxs-lookup"><span data-stu-id="ba273-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="ba273-104">手動 RenderTreeBuilder 邏輯</span><span class="sxs-lookup"><span data-stu-id="ba273-104">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="ba273-105"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供操作元件和元素的方法，包括以 c # 程式碼手動建立元件。</span><span class="sxs-lookup"><span data-stu-id="ba273-105"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!WARNING]
> <span data-ttu-id="ba273-106">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 來建立元件是一個 *advanced 案例*。</span><span class="sxs-lookup"><span data-stu-id="ba273-106">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an *advanced scenario*.</span></span> <span data-ttu-id="ba273-107">格式不正確的元件 (例如，未封閉的標記標記) 可能會導致未定義的行為。</span><span class="sxs-lookup"><span data-stu-id="ba273-107">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span> <span data-ttu-id="ba273-108">未定義的行為包括中斷的內容轉譯、應用程式功能遺失，以及 **_洩漏的安全性_**。</span><span class="sxs-lookup"><span data-stu-id="ba273-108">Undefined behavior includes broken content rendering, loss of app features, and **_compromised security_**.</span></span>

<span data-ttu-id="ba273-109">請考慮下列 `PetDetails` 元件，該元件可以在另一個元件中手動呈現。</span><span class="sxs-lookup"><span data-stu-id="ba273-109">Consider the following `PetDetails` component, which can be manually rendered in another component.</span></span>

<span data-ttu-id="ba273-110">`Shared/PetDetails.razor`:</span><span class="sxs-lookup"><span data-stu-id="ba273-110">`Shared/PetDetails.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

<span data-ttu-id="ba273-111">在下列 `BuiltContent` 元件中，方法中的迴圈會 `CreateComponent` 產生三個 `PetDetails` 元件。</span><span class="sxs-lookup"><span data-stu-id="ba273-111">In the following `BuiltContent` component, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span>

<span data-ttu-id="ba273-112">在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 具有序號的方法中，序號是源程式碼號。</span><span class="sxs-lookup"><span data-stu-id="ba273-112">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="ba273-113">Blazor差異演算法依賴不同行程式碼（而不是不同的呼叫調用）對應的序號。</span><span class="sxs-lookup"><span data-stu-id="ba273-113">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="ba273-114">使用方法建立元件時 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ，請將序號的引數硬式編碼。</span><span class="sxs-lookup"><span data-stu-id="ba273-114">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="ba273-115">**使用計算或計數器來產生序號可能會導致效能不佳。**</span><span class="sxs-lookup"><span data-stu-id="ba273-115">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="ba273-116">如需詳細資訊，請參閱序號與程式 [程式碼號相關，而不是執行順序](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) 一節。</span><span class="sxs-lookup"><span data-stu-id="ba273-116">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="ba273-117">`Pages/BuiltContent.razor`:</span><span class="sxs-lookup"><span data-stu-id="ba273-117">`Pages/BuiltContent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

> [!WARNING]
> <span data-ttu-id="ba273-118">中的類型 <xref:Microsoft.AspNetCore.Components.RenderTree> 允許處理轉譯作業的 *結果* 。</span><span class="sxs-lookup"><span data-stu-id="ba273-118">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="ba273-119">這些是架構執行的內部詳細資料 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="ba273-119">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="ba273-120">這些類型應該視為不 *穩定* ，未來的版本可能會變更。</span><span class="sxs-lookup"><span data-stu-id="ba273-120">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="ba273-121">序號與程式程式碼號和非執行順序相關</span><span class="sxs-lookup"><span data-stu-id="ba273-121">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="ba273-122">Razor 元件檔 (`.razor`) 一律會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="ba273-122">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="ba273-123">執行已編譯的程式碼對解讀程式碼有潛在的優勢，因為產生已編譯器代碼的編譯步驟可以用來插入資訊，以改善執行時間的應用程式效能。</span><span class="sxs-lookup"><span data-stu-id="ba273-123">Executing compiled code has a potential advantage over interpreting code because the compile step that yields the compiled code can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="ba273-124">這些改進的主要範例包含 *序號*。</span><span class="sxs-lookup"><span data-stu-id="ba273-124">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="ba273-125">序號會向執行時間指出輸出來自哪些不同和已排序的程式程式碼。</span><span class="sxs-lookup"><span data-stu-id="ba273-125">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="ba273-126">執行時間會使用這項資訊，以線性時間產生有效率的樹狀結構差異，這遠比一般樹狀結構差異演算法一般可能更快。</span><span class="sxs-lookup"><span data-stu-id="ba273-126">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="ba273-127">請考慮下列 Razor 元件檔 (`.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="ba273-127">Consider the following Razor component file (`.razor`):</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="ba273-128">上述的 Razor 標記和文字內容會編譯成 c # 程式碼，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ba273-128">The preceding Razor markup and text content compiles into C# code similar to the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="ba273-129">第一次執行程式碼時，產生器 `someFlag` 會 `true` 收到：</span><span class="sxs-lookup"><span data-stu-id="ba273-129">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="ba273-130">順序</span><span class="sxs-lookup"><span data-stu-id="ba273-130">Sequence</span></span> | <span data-ttu-id="ba273-131">類型</span><span class="sxs-lookup"><span data-stu-id="ba273-131">Type</span></span>      | <span data-ttu-id="ba273-132">資料</span><span class="sxs-lookup"><span data-stu-id="ba273-132">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="ba273-133">0</span><span class="sxs-lookup"><span data-stu-id="ba273-133">0</span></span>        | <span data-ttu-id="ba273-134">Text node</span><span class="sxs-lookup"><span data-stu-id="ba273-134">Text node</span></span> | <span data-ttu-id="ba273-135">First</span><span class="sxs-lookup"><span data-stu-id="ba273-135">First</span></span>  |
| <span data-ttu-id="ba273-136">1</span><span class="sxs-lookup"><span data-stu-id="ba273-136">1</span></span>        | <span data-ttu-id="ba273-137">Text node</span><span class="sxs-lookup"><span data-stu-id="ba273-137">Text node</span></span> | <span data-ttu-id="ba273-138">Second</span><span class="sxs-lookup"><span data-stu-id="ba273-138">Second</span></span> |

<span data-ttu-id="ba273-139">想像這 `someFlag` 會變成 `false` ，並再次轉譯標記。</span><span class="sxs-lookup"><span data-stu-id="ba273-139">Imagine that `someFlag` becomes `false` and the markup is rendered again.</span></span> <span data-ttu-id="ba273-140">這次，產生器會收到：</span><span class="sxs-lookup"><span data-stu-id="ba273-140">This time, the builder receives:</span></span>

| <span data-ttu-id="ba273-141">順序</span><span class="sxs-lookup"><span data-stu-id="ba273-141">Sequence</span></span> | <span data-ttu-id="ba273-142">類型</span><span class="sxs-lookup"><span data-stu-id="ba273-142">Type</span></span>       | <span data-ttu-id="ba273-143">資料</span><span class="sxs-lookup"><span data-stu-id="ba273-143">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="ba273-144">1</span><span class="sxs-lookup"><span data-stu-id="ba273-144">1</span></span>        | <span data-ttu-id="ba273-145">Text node</span><span class="sxs-lookup"><span data-stu-id="ba273-145">Text node</span></span>  | <span data-ttu-id="ba273-146">Second</span><span class="sxs-lookup"><span data-stu-id="ba273-146">Second</span></span> |

<span data-ttu-id="ba273-147">當執行時間執行差異時，會看到順序中的專案已 `0` 移除，因此它會使用單一步驟產生下列簡單的 *編輯腳本* ：</span><span class="sxs-lookup"><span data-stu-id="ba273-147">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* with a single step:</span></span>

* <span data-ttu-id="ba273-148">移除第一個文位元組點。</span><span class="sxs-lookup"><span data-stu-id="ba273-148">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="ba273-149">以程式設計方式產生序號的問題</span><span class="sxs-lookup"><span data-stu-id="ba273-149">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="ba273-150">想像一下，您應該改為撰寫下列轉譯樹產生器邏輯：</span><span class="sxs-lookup"><span data-stu-id="ba273-150">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="ba273-151">現在，第一個輸出是：</span><span class="sxs-lookup"><span data-stu-id="ba273-151">Now, the first output is:</span></span>

| <span data-ttu-id="ba273-152">順序</span><span class="sxs-lookup"><span data-stu-id="ba273-152">Sequence</span></span> | <span data-ttu-id="ba273-153">類型</span><span class="sxs-lookup"><span data-stu-id="ba273-153">Type</span></span>      | <span data-ttu-id="ba273-154">資料</span><span class="sxs-lookup"><span data-stu-id="ba273-154">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="ba273-155">0</span><span class="sxs-lookup"><span data-stu-id="ba273-155">0</span></span>        | <span data-ttu-id="ba273-156">Text node</span><span class="sxs-lookup"><span data-stu-id="ba273-156">Text node</span></span> | <span data-ttu-id="ba273-157">First</span><span class="sxs-lookup"><span data-stu-id="ba273-157">First</span></span>  |
| <span data-ttu-id="ba273-158">1</span><span class="sxs-lookup"><span data-stu-id="ba273-158">1</span></span>        | <span data-ttu-id="ba273-159">Text node</span><span class="sxs-lookup"><span data-stu-id="ba273-159">Text node</span></span> | <span data-ttu-id="ba273-160">Second</span><span class="sxs-lookup"><span data-stu-id="ba273-160">Second</span></span> |

<span data-ttu-id="ba273-161">此結果與先前的案例相同，因此不會有負面問題。</span><span class="sxs-lookup"><span data-stu-id="ba273-161">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="ba273-162">`someFlag` 是 `false` 在第二個轉譯上，輸出為：</span><span class="sxs-lookup"><span data-stu-id="ba273-162">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="ba273-163">順序</span><span class="sxs-lookup"><span data-stu-id="ba273-163">Sequence</span></span> | <span data-ttu-id="ba273-164">類型</span><span class="sxs-lookup"><span data-stu-id="ba273-164">Type</span></span>      | <span data-ttu-id="ba273-165">資料</span><span class="sxs-lookup"><span data-stu-id="ba273-165">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="ba273-166">0</span><span class="sxs-lookup"><span data-stu-id="ba273-166">0</span></span>        | <span data-ttu-id="ba273-167">Text node</span><span class="sxs-lookup"><span data-stu-id="ba273-167">Text node</span></span> | <span data-ttu-id="ba273-168">Second</span><span class="sxs-lookup"><span data-stu-id="ba273-168">Second</span></span> |

<span data-ttu-id="ba273-169">這次，diff 演算法會看到 *兩個* 變更已發生。</span><span class="sxs-lookup"><span data-stu-id="ba273-169">This time, the diff algorithm sees that *two* changes have occurred.</span></span> <span data-ttu-id="ba273-170">此演算法會產生下列編輯腳本：</span><span class="sxs-lookup"><span data-stu-id="ba273-170">The algorithm generates the following edit script:</span></span>

* <span data-ttu-id="ba273-171">將第一個文位元組點的值變更為 `Second` 。</span><span class="sxs-lookup"><span data-stu-id="ba273-171">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="ba273-172">移除第二個文位元組點。</span><span class="sxs-lookup"><span data-stu-id="ba273-172">Remove the second text node.</span></span>

<span data-ttu-id="ba273-173">產生序號已遺失有關 `if/else` 原始程式碼中的分支和迴圈出現位置的所有實用資訊。</span><span class="sxs-lookup"><span data-stu-id="ba273-173">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="ba273-174">這會導致差異 **兩次（只要** 前面）。</span><span class="sxs-lookup"><span data-stu-id="ba273-174">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="ba273-175">這是一個簡單的範例。</span><span class="sxs-lookup"><span data-stu-id="ba273-175">This is a trivial example.</span></span> <span data-ttu-id="ba273-176">在更實際的案例中，如果是複雜且深層的嵌套結構，特別是迴圈，則效能成本通常較高。</span><span class="sxs-lookup"><span data-stu-id="ba273-176">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="ba273-177">差異演算法必須在轉譯樹狀結構中進行深度遞迴，而不是立即識別已插入或移除的迴圈區塊或分支。</span><span class="sxs-lookup"><span data-stu-id="ba273-177">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm must recurse deeply into the render trees.</span></span> <span data-ttu-id="ba273-178">這通常會導致建立較長的編輯腳本，因為 diff 演算法 misinformed 了舊的和新的結構彼此之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="ba273-178">This usually results in building longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="ba273-179">指引和結論</span><span class="sxs-lookup"><span data-stu-id="ba273-179">Guidance and conclusions</span></span>

* <span data-ttu-id="ba273-180">如果序號是動態產生的，應用程式效能就會受到影響。</span><span class="sxs-lookup"><span data-stu-id="ba273-180">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="ba273-181">架構無法在執行時間自動建立自己的序號，因為必要的資訊不存在，除非它是在編譯時期進行捕捉。</span><span class="sxs-lookup"><span data-stu-id="ba273-181">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="ba273-182">請勿撰寫很長的手動執行 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 邏輯區塊。</span><span class="sxs-lookup"><span data-stu-id="ba273-182">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="ba273-183">偏好使用檔案 `.razor` ，並讓編譯器處理序號。</span><span class="sxs-lookup"><span data-stu-id="ba273-183">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="ba273-184">如果您無法避免手動 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 邏輯，請將很長的程式碼區塊分割成包裝在呼叫中的較小部分 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ba273-184">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="ba273-185">每個區域都有自己的序號不同的空間，因此您可以從零 (或在每個區域內) 任何其他任意數目的任一數字重新開機。</span><span class="sxs-lookup"><span data-stu-id="ba273-185">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="ba273-186">如果序號是硬式編碼，則差異演算法只會要求序號增加值。</span><span class="sxs-lookup"><span data-stu-id="ba273-186">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="ba273-187">初始值與間距無關。</span><span class="sxs-lookup"><span data-stu-id="ba273-187">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="ba273-188">其中一個合法的選項是使用程式程式碼號作為序號，或從零開始，並依一個或數百個 (或任何偏好的間隔) 來增加。</span><span class="sxs-lookup"><span data-stu-id="ba273-188">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span>
* <span data-ttu-id="ba273-189">Blazor 使用序號，其他樹狀結構比較的 UI 架構則不會使用它們。</span><span class="sxs-lookup"><span data-stu-id="ba273-189">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="ba273-190">使用序號時，比較會更快，且 Blazor 具有可自動處理開發人員撰寫檔案之序號的編譯步驟 `.razor` 。</span><span class="sxs-lookup"><span data-stu-id="ba273-190">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
