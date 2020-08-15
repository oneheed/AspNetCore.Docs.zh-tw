---
title: ASP.NET Core 中的測試元件 Blazor
author: guardrex
description: 瞭解如何測試應用程式中的 componments Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 30c5ead98c5da934c1e76577c5dc1a39c7224a79
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253715"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a><span data-ttu-id="dbdf2-103">ASP.NET Core 中的測試元件 Blazor</span><span class="sxs-lookup"><span data-stu-id="dbdf2-103">Test components in ASP.NET Core Blazor</span></span>

[<span data-ttu-id="dbdf2-104">Egil Hansen</span><span class="sxs-lookup"><span data-stu-id="dbdf2-104">Egil Hansen</span></span>](https://egilhansen.com/)

<span data-ttu-id="dbdf2-105">測試是建立穩定和可維護軟體的重要層面。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="dbdf2-106">若要測試 Blazor 元件， *受測試的元件* (剪下) 是：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="dbdf2-107">以測試的相關輸入呈現。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="dbdf2-108">視所執行的測試類型而定，可能會受到互動或修改。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="dbdf2-109">例如，可以觸發事件處理常式，例如 `onclick` 按鈕的事件。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="dbdf2-110">檢查是否有預期的值。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="dbdf2-111">測試方法</span><span class="sxs-lookup"><span data-stu-id="dbdf2-111">Test approaches</span></span>

<span data-ttu-id="dbdf2-112">測試元件的兩個常見方法 Blazor 是端對端 (E2E) 測試和單元測試：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="dbdf2-113">**單元測試**： [單元測試](/dotnet/core/testing/) 是以提供下列內容的單元測試程式庫撰寫：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="dbdf2-114">元件轉譯。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-114">Component rendering.</span></span>
  * <span data-ttu-id="dbdf2-115">檢查元件輸出和狀態。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="dbdf2-116">觸發事件處理常式和生命週期方法。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="dbdf2-117">元件行為正確的判斷提示。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="dbdf2-118">[bUnit](https://github.com/egil/bUnit) 是可啟用元件單元測試的程式庫範例 Razor 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="dbdf2-119">**E2E 測試**：測試執行器會執行 Blazor 應用程式，其中包含剪下並自動執行瀏覽器實例。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="dbdf2-120">測試控管會透過瀏覽器檢查並與剪下互動。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="dbdf2-121">[Selenium](https://github.com/SeleniumHQ/selenium) 是可搭配應用程式使用的 E2E 測試架構範例 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="dbdf2-122">在單元測試中，只 Blazor 涉及 (Razor /c # ) 的元件。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="dbdf2-123">外部相依性（例如服務和 JS interop）必須是模擬。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="dbdf2-124">在 E2E 測試中， Blazor 元件和其所有輔助基礎結構都是測試的一部分，包括 CSS、JS 和 DOM 和瀏覽器 api。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="dbdf2-125">*測試範圍* 會描述測試的廣泛程度。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="dbdf2-126">測試範圍通常會影響測試的速度。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="dbdf2-127">單元測試會在應用程式子系統的子集上執行，而且通常會以毫秒來執行。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="dbdf2-128">E2E 測試：測試應用程式子系統的廣泛群組，可能需要幾秒鐘的時間才能完成。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="dbdf2-129">單元測試也可以存取剪下的實例，以允許檢查和驗證元件的內部狀態。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="dbdf2-130">這通常無法在 E2E 測試中進行。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="dbdf2-131">相對於元件的環境，E2E 測試必須確定已達到預期的環境狀態，然後才會開始驗證。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="dbdf2-132">否則，結果會是無法預測的。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="dbdf2-133">在單元測試中，測試剪下和生命週期的呈現更為整合，這可改善測試穩定性。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="dbdf2-134">E2E 測試牽涉到啟動多個進程、網路和磁片 i/o，以及其他經常導致測試可靠性不佳的子系統活動。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="dbdf2-135">單元測試通常不會受到這類問題的隔離。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="dbdf2-136">下表摘要說明這兩種測試方法之間的差異。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="dbdf2-137">功能</span><span class="sxs-lookup"><span data-stu-id="dbdf2-137">Capability</span></span>                       | <span data-ttu-id="dbdf2-138">單元測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-138">Unit testing</span></span>                     | <span data-ttu-id="dbdf2-139">E2E 測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="dbdf2-140">測試範圍</span><span class="sxs-lookup"><span data-stu-id="dbdf2-140">Test scope</span></span>                       | <span data-ttu-id="dbdf2-141">Blazor 元件 (Razor /c # 僅 ) </span><span class="sxs-lookup"><span data-stu-id="dbdf2-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="dbdf2-142">BlazorRazor具有 CSS/JS 的元件 (/c # ) </span><span class="sxs-lookup"><span data-stu-id="dbdf2-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="dbdf2-143">測試回合時間</span><span class="sxs-lookup"><span data-stu-id="dbdf2-143">Test execution time</span></span>              | <span data-ttu-id="dbdf2-144">毫秒</span><span class="sxs-lookup"><span data-stu-id="dbdf2-144">Milliseconds</span></span>                     | <span data-ttu-id="dbdf2-145">秒</span><span class="sxs-lookup"><span data-stu-id="dbdf2-145">Seconds</span></span>                                 |
| <span data-ttu-id="dbdf2-146">元件實例的存取權</span><span class="sxs-lookup"><span data-stu-id="dbdf2-146">Access to the component instance</span></span> | <span data-ttu-id="dbdf2-147">是</span><span class="sxs-lookup"><span data-stu-id="dbdf2-147">Yes</span></span>                              | <span data-ttu-id="dbdf2-148">否</span><span class="sxs-lookup"><span data-stu-id="dbdf2-148">No</span></span>                                      |
| <span data-ttu-id="dbdf2-149">對環境的敏感性</span><span class="sxs-lookup"><span data-stu-id="dbdf2-149">Sensitive to the environment</span></span>     | <span data-ttu-id="dbdf2-150">否</span><span class="sxs-lookup"><span data-stu-id="dbdf2-150">No</span></span>                               | <span data-ttu-id="dbdf2-151">是</span><span class="sxs-lookup"><span data-stu-id="dbdf2-151">Yes</span></span>                                     |
| <span data-ttu-id="dbdf2-152">可靠性</span><span class="sxs-lookup"><span data-stu-id="dbdf2-152">Reliability</span></span>                      | <span data-ttu-id="dbdf2-153">更可靠</span><span class="sxs-lookup"><span data-stu-id="dbdf2-153">More reliable</span></span>                    | <span data-ttu-id="dbdf2-154">較不可靠</span><span class="sxs-lookup"><span data-stu-id="dbdf2-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="dbdf2-155">選擇最適當的測試方法</span><span class="sxs-lookup"><span data-stu-id="dbdf2-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="dbdf2-156">選擇要執行的測試類型時，請考慮這種情況。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="dbdf2-157">下表說明一些考慮。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="dbdf2-158">狀況</span><span class="sxs-lookup"><span data-stu-id="dbdf2-158">Scenario</span></span> | <span data-ttu-id="dbdf2-159">建議的方法</span><span class="sxs-lookup"><span data-stu-id="dbdf2-159">Suggested approach</span></span> | <span data-ttu-id="dbdf2-160">備註</span><span class="sxs-lookup"><span data-stu-id="dbdf2-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="dbdf2-161">沒有 JS interop 邏輯的元件</span><span class="sxs-lookup"><span data-stu-id="dbdf2-161">Component without JS interop logic</span></span> | <span data-ttu-id="dbdf2-162">單元測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-162">Unit testing</span></span> | <span data-ttu-id="dbdf2-163">當元件中沒有 JS interop 的相依性時 Blazor ，可以在不存取 js 或 DOM API 的情況下測試元件。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="dbdf2-164">在此案例中，選擇單元測試沒有缺點。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="dbdf2-165">具有簡單 JS interop 邏輯的元件</span><span class="sxs-lookup"><span data-stu-id="dbdf2-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="dbdf2-166">單元測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-166">Unit testing</span></span> | <span data-ttu-id="dbdf2-167">元件通常是透過 JS interop 來查詢 DOM 或觸發動畫。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="dbdf2-168">在此案例中，通常會偏好進行單元測試，因為透過介面模擬 JS 互動十分簡單 <xref:Microsoft.JSInterop.IJSRuntime> 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="dbdf2-169">相依于複雜 JS 程式碼的元件</span><span class="sxs-lookup"><span data-stu-id="dbdf2-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="dbdf2-170">單元測試和獨立的 JS 測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="dbdf2-171">如果元件使用 JS interop 呼叫大型或複雜的 JS 程式庫 Blazor ，但元件和 js 程式庫之間的互動很簡單，那麼最好的方法可能會將元件和 js 程式庫或程式碼視為兩個不同的部分，並個別進行測試。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="dbdf2-172">Blazor使用單元測試程式庫測試元件，並使用 js 測試程式庫測試 js。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="dbdf2-173">具有相依于瀏覽器 DOM 之 JS 操作的邏輯元件</span><span class="sxs-lookup"><span data-stu-id="dbdf2-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="dbdf2-174">E2E 測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-174">E2E testing</span></span> | <span data-ttu-id="dbdf2-175">當元件的功能相依于 JS 並操作 DOM 時，請在 E2E 測試中同時驗證 JS 和 Blazor 程式碼。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="dbdf2-176">這是 Blazor 架構開發人員使用 Blazor 的瀏覽器呈現邏輯所採用的方法，其具有緊密結合的 c # 和 JS 程式碼。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="dbdf2-177">C # 和 JS 程式碼必須一起運作，才能 Blazor 在瀏覽器中正確呈現元件。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="dbdf2-178">相依于協力廠商元件程式庫並具有硬式模擬相關性的元件</span><span class="sxs-lookup"><span data-stu-id="dbdf2-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="dbdf2-179">E2E 測試</span><span class="sxs-lookup"><span data-stu-id="dbdf2-179">E2E testing</span></span> | <span data-ttu-id="dbdf2-180">當元件的功能相依于具有難以模擬之相依性的協力廠商元件程式庫（例如 JS interop）時，E2E 測試可能是測試元件的唯一選項。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="dbdf2-181">使用 bUnit 測試元件</span><span class="sxs-lookup"><span data-stu-id="dbdf2-181">Test components with bUnit</span></span>

<span data-ttu-id="dbdf2-182">沒有適用于的官方 Microsoft 測試架構 Blazor ，但以社區為導向的專案 [bUnit](https://github.com/egil/bUnit) 提供便利的方式來進行單元測試 Blazor 元件。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="dbdf2-183">bUnit 是協力廠商測試程式庫，並不受 Microsoft 支援或維護。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="dbdf2-184">bUnit 適用于一般用途的測試架構，例如 [MSTest](/dotnet/core/testing/unit-testing-with-mstest)、 [NUnit](https://nunit.org/)和 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="dbdf2-185">這些測試架構讓 bUnit 測試看起來像平常的單元測試。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="dbdf2-186">與一般用途測試架構整合的 bUnit 測試，通常會以執行：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="dbdf2-187">[Visual Studio 的測試瀏覽器](/visualstudio/test/run-unit-tests-with-test-explorer)。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="dbdf2-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) 命令 shell 中的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="dbdf2-189">自動化的 DevOps 測試管線。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="dbdf2-190">跨不同測試架構的測試概念和測試執行類似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="dbdf2-191">如需指導方針，請參閱測試架構的檔。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="dbdf2-192">下列範例會根據 `Counter` 專案範本，示範應用程式中元件的 bUnit 測試結構 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="dbdf2-193">`Counter`元件會根據使用者選取頁面中的按鈕，顯示並遞增計數器：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="dbdf2-194">下列 bUnit 測試會在選取按鈕時，確認剪下的計數器是否已正確遞增：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="dbdf2-195">下列動作會在測試的每個步驟中進行：</span><span class="sxs-lookup"><span data-stu-id="dbdf2-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="dbdf2-196">*排列*： `Counter` 元件會使用 bUnit 的來呈現 `TestContext` 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="dbdf2-197">已找到剪下的段落元素 (`<p>`) ，並將其指派給 `paraElm` 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="dbdf2-198">*Act*： () 中的按鈕元素，然後藉 `<button>` 由呼叫來加以選取 `Click` ，這應該會遞增計數器，並更新 () 之段落標記的內容 `<p>` 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="dbdf2-199">藉由呼叫來取得段落元素文字內容 `TextContent` 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="dbdf2-200">*Assert*： `MarkupMatches` 在文字內容上呼叫，以確認它符合預期的字串，也就是 `Current count: 1` 。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="dbdf2-201">`MarkupMatches`Assert 方法與一般字串比較判斷提示不同 (例如，) 會 `Assert.Equal("Current count: 1", paraElmText);` `MarkupMatches` 執行輸入和預期 HTML 標籤的語義比較。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="dbdf2-202">語義比較會感知 HTML 語義，這表示會忽略不重要的空白字元之類的專案。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="dbdf2-203">這會導致更穩定的測試。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-203">This results in more stable tests.</span></span> <span data-ttu-id="dbdf2-204">如需詳細資訊，請參閱 [自訂語義 HTML 比較](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbdf2-205">其他資源</span><span class="sxs-lookup"><span data-stu-id="dbdf2-205">Additional resources</span></span>

* <span data-ttu-id="dbdf2-206">[具有 bUnit 的消費者入門](https://bunit.egilhansen.com/docs/getting-started/)： bUnit 指示包含建立測試專案、參考測試架構封裝，以及建立和執行測試的指引。</span><span class="sxs-lookup"><span data-stu-id="dbdf2-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
