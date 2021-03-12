---
title: 測試 ASP.NET Core 中的控制器邏輯
author: ardalis
description: 了解如何使用 Moq 和 xUnit 測試 ASP.NET Core 中的控制器邏輯。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 0f4fcceefdef946a5f29633c069cbb64ec345a3d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586952"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a><span data-ttu-id="61a88-103">ASP.NET Core 中的單元測試控制器邏輯</span><span class="sxs-lookup"><span data-stu-id="61a88-103">Unit test controller logic in ASP.NET Core</span></span>

<span data-ttu-id="61a88-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="61a88-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="61a88-105">[單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)包括將應用程式的一部分與其基礎結構和相依性隔離測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-105">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="61a88-106">對控制器邏輯進行單元測試時，只會測試單一動作的內容，而不會測試其相依性或架構本身的行為。</span><span class="sxs-lookup"><span data-stu-id="61a88-106">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

## <a name="unit-testing-controllers"></a><span data-ttu-id="61a88-107">單元測試控制器</span><span class="sxs-lookup"><span data-stu-id="61a88-107">Unit testing controllers</span></span>

<span data-ttu-id="61a88-108">設定單元測試的控制器動作，以專注於控制器的行為。</span><span class="sxs-lookup"><span data-stu-id="61a88-108">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="61a88-109">控制器單元測試會避開[篩選](xref:mvc/controllers/filters)、[路由](xref:fundamentals/routing)和[模型繫結](xref:mvc/models/model-binding)等情況。</span><span class="sxs-lookup"><span data-stu-id="61a88-109">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="61a88-110">涵蓋共同回應要求之元件之間互動的測試，都由 *整合測試* 處理。</span><span class="sxs-lookup"><span data-stu-id="61a88-110">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="61a88-111">如需整合測試的詳細資訊，請參閱 <xref:test/integration-tests>。</span><span class="sxs-lookup"><span data-stu-id="61a88-111">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="61a88-112">如果您想要撰寫自訂篩選和路由，請單獨對其進行單元測試，而不是當作特定控制器動作測試的一部分。</span><span class="sxs-lookup"><span data-stu-id="61a88-112">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="61a88-113">若要進行控制器單元測試，請檢閱下列範例應用程式中的控制器。</span><span class="sxs-lookup"><span data-stu-id="61a88-113">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> 

<span data-ttu-id="61a88-114">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="61a88-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="61a88-115">主控制器會顯示一份腦力激盪工作階段清單，並允許使用 POST 要求來建立新的腦力激盪工作階段：</span><span class="sxs-lookup"><span data-stu-id="61a88-115">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="61a88-116">上述控制器會：</span><span class="sxs-lookup"><span data-stu-id="61a88-116">The preceding controller:</span></span>

* <span data-ttu-id="61a88-117">遵循[明確相依性準則](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="61a88-117">Follows the [Explicit Dependencies Principle](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="61a88-118">預期[相依性插入 (DI)](xref:fundamentals/dependency-injection) 以提供 `IBrainstormSessionRepository` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="61a88-118">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="61a88-119">可以使用模擬物件架構 (例如 [Moq](https://www.nuget.org/packages/Moq/)) 透過模擬 `IBrainstormSessionRepository` 服務進行測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-119">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="61a88-120">「模擬物件」是製作出來的物件，具有一組用於測試的預定屬性和方法行為。</span><span class="sxs-lookup"><span data-stu-id="61a88-120">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="61a88-121">如需詳細資訊，請參閱[整合測試簡介](xref:test/integration-tests#introduction-to-integration-tests)。</span><span class="sxs-lookup"><span data-stu-id="61a88-121">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="61a88-122">`HTTP GET Index` 方法有沒有迴圈或分支，而且只會呼叫一個方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-122">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="61a88-123">此動作的單元測試：</span><span class="sxs-lookup"><span data-stu-id="61a88-123">The unit test for this action:</span></span>

* <span data-ttu-id="61a88-124">使用 `IBrainstormSessionRepository` 方法來模擬 `GetTestSessions` 服務。</span><span class="sxs-lookup"><span data-stu-id="61a88-124">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="61a88-125">`GetTestSessions` 會建立兩個具有日期和工作階段名稱的模擬腦力激盪工作階段。</span><span class="sxs-lookup"><span data-stu-id="61a88-125">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="61a88-126">執行 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-126">Executes the `Index` method.</span></span>
* <span data-ttu-id="61a88-127">對方法傳回的結果進行判斷提示：</span><span class="sxs-lookup"><span data-stu-id="61a88-127">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="61a88-128">會傳回 <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-128">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="61a88-129">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) 是 `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="61a88-129">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="61a88-130">在 `ViewDataDictionary.Model` 中有兩個腦力激盪工作階段。</span><span class="sxs-lookup"><span data-stu-id="61a88-130">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="61a88-131">主控制器的 `HTTP POST Index` 方法測試會驗證：</span><span class="sxs-lookup"><span data-stu-id="61a88-131">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="61a88-132">當 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) 為 `false` 時，動作方法會傳回具有適當資料的 *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-132">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="61a88-133">當 `ModelState.IsValid` 為 `true`：</span><span class="sxs-lookup"><span data-stu-id="61a88-133">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="61a88-134">會呼叫 `Add` 存放庫上的方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-134">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="61a88-135">會傳回具有正確引數的 <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-135">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="61a88-136">您可以使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 新增錯誤，來測試無效的模型狀態，如下列第一項測試中所示：</span><span class="sxs-lookup"><span data-stu-id="61a88-136">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="61a88-137">當 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 無效時，會傳回與 GET 要求相同的 `ViewResult`。</span><span class="sxs-lookup"><span data-stu-id="61a88-137">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="61a88-138">此測試不會嘗試傳入無效的模型。</span><span class="sxs-lookup"><span data-stu-id="61a88-138">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="61a88-139">傳遞無效模型不是有效的方法，因為模型繫結並未執行 (儘管[整合測試](xref:test/integration-tests)確實使用模型繫結)。</span><span class="sxs-lookup"><span data-stu-id="61a88-139">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="61a88-140">在此情況下，不會測試模型繫結。</span><span class="sxs-lookup"><span data-stu-id="61a88-140">In this case, model binding isn't tested.</span></span> <span data-ttu-id="61a88-141">這些單元測試只會測試動作方法中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="61a88-141">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="61a88-142">第二項測試會驗證 `ModelState` 何時有效：</span><span class="sxs-lookup"><span data-stu-id="61a88-142">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="61a88-143">新增 `BrainstormSession` (透過存放庫)。</span><span class="sxs-lookup"><span data-stu-id="61a88-143">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="61a88-144">此方法會以預期屬性傳回 `RedirectToActionResult`。</span><span class="sxs-lookup"><span data-stu-id="61a88-144">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="61a88-145">通常會忽略未呼叫的模擬呼叫，但在安裝程式呼叫結束時呼叫 `Verifiable` 即可在測試中對其進行模擬驗證。</span><span class="sxs-lookup"><span data-stu-id="61a88-145">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="61a88-146">可透過呼叫 `mockRepo.Verify` 來執行，如果未呼叫預期的方法，則測試會失敗。</span><span class="sxs-lookup"><span data-stu-id="61a88-146">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="61a88-147">此範例中所使用的 Moq 程式庫，可讓您混合可驗證 (或「嚴格」) 的模擬，以及無法驗證的模擬 (也稱為「鬆散」的模擬或虛設常式)。</span><span class="sxs-lookup"><span data-stu-id="61a88-147">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="61a88-148">如需詳細資訊，請參閱 [Customizing Mock Behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior) (使用 Moq 自訂模擬行為)。</span><span class="sxs-lookup"><span data-stu-id="61a88-148">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="61a88-149">範例應用程式中的 [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs)會顯示與特定腦力激盪工作階段相關的資訊。</span><span class="sxs-lookup"><span data-stu-id="61a88-149">[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="61a88-150">控制器包含處理無效 `id` 值的邏輯 (下列範例中有兩個 `return` 案例來說明這些案例)。</span><span class="sxs-lookup"><span data-stu-id="61a88-150">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="61a88-151">最後的 `return` 陳述式會將新的 `StormSessionViewModel` 傳回至檢視 (*Controllers/SessionController.cs*)：</span><span class="sxs-lookup"><span data-stu-id="61a88-151">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="61a88-152">單元測試包含工作階段控制器 `Index` 動作中每個 `return` 案例的一項測試：</span><span class="sxs-lookup"><span data-stu-id="61a88-152">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="61a88-153">當移動至構想控制器，應用程式會將功能公開為 `api/ideas` 路徑上的 Web API：</span><span class="sxs-lookup"><span data-stu-id="61a88-153">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="61a88-154">`ForSession` 方法會傳回與腦力激盪工作階段建立關聯的構想清單 (`IdeaDTO`)。</span><span class="sxs-lookup"><span data-stu-id="61a88-154">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="61a88-155">`Create` 方法會將新構想新增至工作階段。</span><span class="sxs-lookup"><span data-stu-id="61a88-155">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="61a88-156">請避免直接透過 API 呼叫來傳回商務網域實體。</span><span class="sxs-lookup"><span data-stu-id="61a88-156">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="61a88-157">領域實體：</span><span class="sxs-lookup"><span data-stu-id="61a88-157">Domain entities:</span></span>

* <span data-ttu-id="61a88-158">通常會包含超過用戶端所需的資料。</span><span class="sxs-lookup"><span data-stu-id="61a88-158">Often include more data than the client requires.</span></span>
* <span data-ttu-id="61a88-159">會不必要地將應用程式內部網域模型與公用公開的 API 結合在一起。</span><span class="sxs-lookup"><span data-stu-id="61a88-159">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="61a88-160">可以執行網域實體與傳回給用戶端之類型間的對應：</span><span class="sxs-lookup"><span data-stu-id="61a88-160">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="61a88-161">透過 LINQ `Select` 手動進行，如範例應用程式使用的方式一樣。</span><span class="sxs-lookup"><span data-stu-id="61a88-161">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="61a88-162">如需詳細資訊，請參閱 [LINQ (Language Integrated Query)](/dotnet/standard/using-linq)。</span><span class="sxs-lookup"><span data-stu-id="61a88-162">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="61a88-163">透過程式庫自動進行，例如 [AutoMapper](https://github.com/AutoMapper/AutoMapper)。</span><span class="sxs-lookup"><span data-stu-id="61a88-163">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="61a88-164">接下來，範例應用程式會示範構想控制器 `Create` 和 `ForSession` API 方法的單元測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-164">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="61a88-165">範例應用程式包含兩項 `ForSession` 測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-165">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="61a88-166">第一項測試會判斷 `ForSession` 是否會為無效的工作階段傳回 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (找不到 HTTP)：</span><span class="sxs-lookup"><span data-stu-id="61a88-166">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="61a88-167">第二項 `ForSession` 測試會判斷 `ForSession` 是否會傳有效工作階段的工作階段構想清單 (`<List<IdeaDTO>>`)。</span><span class="sxs-lookup"><span data-stu-id="61a88-167">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="61a88-168">檢查也會查驗第一個構想，以確認其 `Name` 屬性是否正確：</span><span class="sxs-lookup"><span data-stu-id="61a88-168">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="61a88-169">為了測試 `ModelState` 無效時的 `Create` 方法行為，範例應用程式會將模型錯誤新增至控制器作為測試的一部分。</span><span class="sxs-lookup"><span data-stu-id="61a88-169">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="61a88-170">請勿嘗試在單元測試中測試模型驗證或模型繫結&mdash;只要測試當遇到無效 `ModelState` 時的動作方法行為即可：</span><span class="sxs-lookup"><span data-stu-id="61a88-170">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="61a88-171">第二項 `Create` 測試需要儲存機制傳回 `null`，因此會設定模擬儲存機制傳回 `null`。</span><span class="sxs-lookup"><span data-stu-id="61a88-171">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="61a88-172">不需要建立測試資料庫 (在記憶體中或其他位置)，也不需要建構傳回此結果的查詢。</span><span class="sxs-lookup"><span data-stu-id="61a88-172">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="61a88-173">測試可在單一陳述式中完成，如範例程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="61a88-173">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="61a88-174">第三項 `Create` 測試 (`Create_ReturnsNewlyCreatedIdeaForSession`) 會確認是否呼叫存放庫的 `UpdateAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-174">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="61a88-175">會使用 `Verifiable` 呼叫模擬，再呼叫模擬存放庫的 `Verify` 方法，確認是否已執行可驗證的方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-175">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="61a88-176">單元測試無須負責確保 `UpdateAsync` 方法已儲存資料&mdash;而是透過整合測試來完成。</span><span class="sxs-lookup"><span data-stu-id="61a88-176">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="61a88-177">測試 ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="61a88-177">Test ActionResult\<T></span></span>

<span data-ttu-id="61a88-178">在 ASP.NET Core 2.1 或更新版本中， [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) 可讓您傳回衍生自 `ActionResult` 或傳回特定型別的型別。</span><span class="sxs-lookup"><span data-stu-id="61a88-178">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="61a88-179">範例應用程式包含為指定工作階段 `id` 傳回 `List<IdeaDTO>` 的方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-179">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="61a88-180">如果工作階段 `id` 不存在，則控制器會傳回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>：</span><span class="sxs-lookup"><span data-stu-id="61a88-180">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="61a88-181">`ForSessionActionResult` 控制器的兩項測試會包含於 `ApiIdeasControllerTests`。</span><span class="sxs-lookup"><span data-stu-id="61a88-181">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="61a88-182">第一項測試會確認控制器傳回 `ActionResult`，而不是為不存在之工作階段 `id` 傳回不存在的構想清單：</span><span class="sxs-lookup"><span data-stu-id="61a88-182">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="61a88-183">`ActionResult` 類型為 `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="61a88-183">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="61a88-184"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> 是 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-184">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="61a88-185">針對有效的工作階段 `id`，第二項測試會確認此方法傳回：</span><span class="sxs-lookup"><span data-stu-id="61a88-185">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="61a88-186">具有 `ActionResult` 類型的 `List<IdeaDTO>`。</span><span class="sxs-lookup"><span data-stu-id="61a88-186">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="61a88-187">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)是一 `List<IdeaDTO>` 種類型。</span><span class="sxs-lookup"><span data-stu-id="61a88-187">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="61a88-188">清單中的第一個項目，是與儲存在模擬工作階段中構想相符的有效構想 (透過呼叫 `GetTestSession` 來取得)。</span><span class="sxs-lookup"><span data-stu-id="61a88-188">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="61a88-189">範例應用程式也包含方法，為指定工作階段建立新的 `Idea`。</span><span class="sxs-lookup"><span data-stu-id="61a88-189">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="61a88-190">控制器會傳回：</span><span class="sxs-lookup"><span data-stu-id="61a88-190">The controller returns:</span></span>

* <span data-ttu-id="61a88-191"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> (針對無效的模型)。</span><span class="sxs-lookup"><span data-stu-id="61a88-191"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="61a88-192"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> (如果工作階段不存在)。</span><span class="sxs-lookup"><span data-stu-id="61a88-192"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="61a88-193"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> (以新構想更新工作階段時)。</span><span class="sxs-lookup"><span data-stu-id="61a88-193"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="61a88-194">`CreateActionResult` 的三項測試都包含在 `ApiIdeasControllerTests` 中。</span><span class="sxs-lookup"><span data-stu-id="61a88-194">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="61a88-195">第一項測試會確認為無效的模型傳回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>。</span><span class="sxs-lookup"><span data-stu-id="61a88-195">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="61a88-196">如果工作階段不存在，第二項測試將檢查是否傳回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>。</span><span class="sxs-lookup"><span data-stu-id="61a88-196">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="61a88-197">針對有效的工作階段 `id`，最終測試會確認：</span><span class="sxs-lookup"><span data-stu-id="61a88-197">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="61a88-198">此方法會以 `BrainstormSession` 類型傳回 `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="61a88-198">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="61a88-199">[ActionResult \<T> 。結果](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)是 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> 。</span><span class="sxs-lookup"><span data-stu-id="61a88-199">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="61a88-200">`CreatedAtActionResult` 類似於具有 `Location` 標頭尸的「201 已建立」回應。</span><span class="sxs-lookup"><span data-stu-id="61a88-200">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="61a88-201">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)是一 `BrainstormSession` 種類型。</span><span class="sxs-lookup"><span data-stu-id="61a88-201">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="61a88-202">已叫用更新工作階段 (`UpdateAsync(testSession)`) 的模擬呼叫。</span><span class="sxs-lookup"><span data-stu-id="61a88-202">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="61a88-203">`Verifiable` 方法呼叫會透過在判斷提示中執行 `mockRepo.Verify()` 來檢查。</span><span class="sxs-lookup"><span data-stu-id="61a88-203">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="61a88-204">會為工作階段傳回兩個 `Idea` 物件。</span><span class="sxs-lookup"><span data-stu-id="61a88-204">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="61a88-205">最後一個項目 (模擬呼叫 `UpdateAsync` 新增的 `Idea`) 會與在測試中新增至工作階段的 `newIdea` 相符。</span><span class="sxs-lookup"><span data-stu-id="61a88-205">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61a88-206">[控制器](xref:mvc/controllers/actions)在任何 ASP.NET Core MVC 應用程式中都扮演重要角色。</span><span class="sxs-lookup"><span data-stu-id="61a88-206">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="61a88-207">因此，您應該確信控制器的行為符合預期。</span><span class="sxs-lookup"><span data-stu-id="61a88-207">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="61a88-208">在將應用程式部署至生產環境前，自動化測試可以偵測錯誤。</span><span class="sxs-lookup"><span data-stu-id="61a88-208">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="61a88-209">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="61a88-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="61a88-210">控制器邏輯的單元測試</span><span class="sxs-lookup"><span data-stu-id="61a88-210">Unit tests of controller logic</span></span>

<span data-ttu-id="61a88-211">[單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)包括將應用程式的一部分與其基礎結構和相依性隔離測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-211">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="61a88-212">對控制器邏輯進行單元測試時，只會測試單一動作的內容，而不會測試其相依性或架構本身的行為。</span><span class="sxs-lookup"><span data-stu-id="61a88-212">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="61a88-213">設定單元測試的控制器動作，以專注於控制器的行為。</span><span class="sxs-lookup"><span data-stu-id="61a88-213">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="61a88-214">控制器單元測試會避開[篩選](xref:mvc/controllers/filters)、[路由](xref:fundamentals/routing)和[模型繫結](xref:mvc/models/model-binding)等情況。</span><span class="sxs-lookup"><span data-stu-id="61a88-214">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="61a88-215">涵蓋共同回應要求之元件之間互動的測試，都由 *整合測試* 處理。</span><span class="sxs-lookup"><span data-stu-id="61a88-215">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="61a88-216">如需整合測試的詳細資訊，請參閱 <xref:test/integration-tests>。</span><span class="sxs-lookup"><span data-stu-id="61a88-216">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="61a88-217">如果您想要撰寫自訂篩選和路由，請單獨對其進行單元測試，而不是當作特定控制器動作測試的一部分。</span><span class="sxs-lookup"><span data-stu-id="61a88-217">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="61a88-218">若要進行控制器單元測試，請檢閱下列範例應用程式中的控制器。</span><span class="sxs-lookup"><span data-stu-id="61a88-218">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="61a88-219">主控制器會顯示一份腦力激盪工作階段清單，並允許使用 POST 要求來建立新的腦力激盪工作階段：</span><span class="sxs-lookup"><span data-stu-id="61a88-219">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="61a88-220">上述控制器會：</span><span class="sxs-lookup"><span data-stu-id="61a88-220">The preceding controller:</span></span>

* <span data-ttu-id="61a88-221">遵循[明確相依性準則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="61a88-221">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="61a88-222">預期[相依性插入 (DI)](xref:fundamentals/dependency-injection) 以提供 `IBrainstormSessionRepository` 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="61a88-222">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="61a88-223">可以使用模擬物件架構 (例如 [Moq](https://www.nuget.org/packages/Moq/)) 透過模擬 `IBrainstormSessionRepository` 服務進行測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-223">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="61a88-224">「模擬物件」是製作出來的物件，具有一組用於測試的預定屬性和方法行為。</span><span class="sxs-lookup"><span data-stu-id="61a88-224">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="61a88-225">如需詳細資訊，請參閱[整合測試簡介](xref:test/integration-tests#introduction-to-integration-tests)。</span><span class="sxs-lookup"><span data-stu-id="61a88-225">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="61a88-226">`HTTP GET Index` 方法有沒有迴圈或分支，而且只會呼叫一個方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-226">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="61a88-227">此動作的單元測試：</span><span class="sxs-lookup"><span data-stu-id="61a88-227">The unit test for this action:</span></span>

* <span data-ttu-id="61a88-228">使用 `IBrainstormSessionRepository` 方法來模擬 `GetTestSessions` 服務。</span><span class="sxs-lookup"><span data-stu-id="61a88-228">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="61a88-229">`GetTestSessions` 會建立兩個具有日期和工作階段名稱的模擬腦力激盪工作階段。</span><span class="sxs-lookup"><span data-stu-id="61a88-229">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="61a88-230">執行 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-230">Executes the `Index` method.</span></span>
* <span data-ttu-id="61a88-231">對方法傳回的結果進行判斷提示：</span><span class="sxs-lookup"><span data-stu-id="61a88-231">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="61a88-232">會傳回 <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-232">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="61a88-233">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) 是 `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="61a88-233">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="61a88-234">在 `ViewDataDictionary.Model` 中有兩個腦力激盪工作階段。</span><span class="sxs-lookup"><span data-stu-id="61a88-234">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="61a88-235">主控制器的 `HTTP POST Index` 方法測試會驗證：</span><span class="sxs-lookup"><span data-stu-id="61a88-235">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="61a88-236">當 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) 為 `false` 時，動作方法會傳回具有適當資料的 *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-236">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="61a88-237">當 `ModelState.IsValid` 為 `true`：</span><span class="sxs-lookup"><span data-stu-id="61a88-237">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="61a88-238">會呼叫 `Add` 存放庫上的方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-238">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="61a88-239">會傳回具有正確引數的 <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-239">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="61a88-240">您可以使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 新增錯誤，來測試無效的模型狀態，如下列第一項測試中所示：</span><span class="sxs-lookup"><span data-stu-id="61a88-240">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="61a88-241">當 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 無效時，會傳回與 GET 要求相同的 `ViewResult`。</span><span class="sxs-lookup"><span data-stu-id="61a88-241">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="61a88-242">此測試不會嘗試傳入無效的模型。</span><span class="sxs-lookup"><span data-stu-id="61a88-242">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="61a88-243">傳遞無效模型不是有效的方法，因為模型繫結並未執行 (儘管[整合測試](xref:test/integration-tests)確實使用模型繫結)。</span><span class="sxs-lookup"><span data-stu-id="61a88-243">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="61a88-244">在此情況下，不會測試模型繫結。</span><span class="sxs-lookup"><span data-stu-id="61a88-244">In this case, model binding isn't tested.</span></span> <span data-ttu-id="61a88-245">這些單元測試只會測試動作方法中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="61a88-245">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="61a88-246">第二項測試會驗證 `ModelState` 何時有效：</span><span class="sxs-lookup"><span data-stu-id="61a88-246">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="61a88-247">新增 `BrainstormSession` (透過存放庫)。</span><span class="sxs-lookup"><span data-stu-id="61a88-247">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="61a88-248">此方法會以預期屬性傳回 `RedirectToActionResult`。</span><span class="sxs-lookup"><span data-stu-id="61a88-248">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="61a88-249">通常會忽略未呼叫的模擬呼叫，但在安裝程式呼叫結束時呼叫 `Verifiable` 即可在測試中對其進行模擬驗證。</span><span class="sxs-lookup"><span data-stu-id="61a88-249">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="61a88-250">可透過呼叫 `mockRepo.Verify` 來執行，如果未呼叫預期的方法，則測試會失敗。</span><span class="sxs-lookup"><span data-stu-id="61a88-250">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="61a88-251">此範例中所使用的 Moq 程式庫，可讓您混合可驗證 (或「嚴格」) 的模擬，以及無法驗證的模擬 (也稱為「鬆散」的模擬或虛設常式)。</span><span class="sxs-lookup"><span data-stu-id="61a88-251">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="61a88-252">如需詳細資訊，請參閱 [Customizing Mock Behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior) (使用 Moq 自訂模擬行為)。</span><span class="sxs-lookup"><span data-stu-id="61a88-252">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="61a88-253">範例應用程式中的 [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs)會顯示與特定腦力激盪工作階段相關的資訊。</span><span class="sxs-lookup"><span data-stu-id="61a88-253">[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="61a88-254">控制器包含處理無效 `id` 值的邏輯 (下列範例中有兩個 `return` 案例來說明這些案例)。</span><span class="sxs-lookup"><span data-stu-id="61a88-254">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="61a88-255">最後的 `return` 陳述式會將新的 `StormSessionViewModel` 傳回至檢視 (*Controllers/SessionController.cs*)：</span><span class="sxs-lookup"><span data-stu-id="61a88-255">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="61a88-256">單元測試包含工作階段控制器 `Index` 動作中每個 `return` 案例的一項測試：</span><span class="sxs-lookup"><span data-stu-id="61a88-256">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="61a88-257">當移動至構想控制器，應用程式會將功能公開為 `api/ideas` 路徑上的 Web API：</span><span class="sxs-lookup"><span data-stu-id="61a88-257">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="61a88-258">`ForSession` 方法會傳回與腦力激盪工作階段建立關聯的構想清單 (`IdeaDTO`)。</span><span class="sxs-lookup"><span data-stu-id="61a88-258">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="61a88-259">`Create` 方法會將新構想新增至工作階段。</span><span class="sxs-lookup"><span data-stu-id="61a88-259">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="61a88-260">請避免直接透過 API 呼叫來傳回商務網域實體。</span><span class="sxs-lookup"><span data-stu-id="61a88-260">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="61a88-261">領域實體：</span><span class="sxs-lookup"><span data-stu-id="61a88-261">Domain entities:</span></span>

* <span data-ttu-id="61a88-262">通常會包含超過用戶端所需的資料。</span><span class="sxs-lookup"><span data-stu-id="61a88-262">Often include more data than the client requires.</span></span>
* <span data-ttu-id="61a88-263">會不必要地將應用程式內部網域模型與公用公開的 API 結合在一起。</span><span class="sxs-lookup"><span data-stu-id="61a88-263">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="61a88-264">可以執行網域實體與傳回給用戶端之類型間的對應：</span><span class="sxs-lookup"><span data-stu-id="61a88-264">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="61a88-265">透過 LINQ `Select` 手動進行，如範例應用程式使用的方式一樣。</span><span class="sxs-lookup"><span data-stu-id="61a88-265">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="61a88-266">如需詳細資訊，請參閱 [LINQ (Language Integrated Query)](/dotnet/standard/using-linq)。</span><span class="sxs-lookup"><span data-stu-id="61a88-266">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="61a88-267">透過程式庫自動進行，例如 [AutoMapper](https://github.com/AutoMapper/AutoMapper)。</span><span class="sxs-lookup"><span data-stu-id="61a88-267">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="61a88-268">接下來，範例應用程式會示範構想控制器 `Create` 和 `ForSession` API 方法的單元測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-268">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="61a88-269">範例應用程式包含兩項 `ForSession` 測試。</span><span class="sxs-lookup"><span data-stu-id="61a88-269">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="61a88-270">第一項測試會判斷 `ForSession` 是否會為無效的工作階段傳回 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (找不到 HTTP)：</span><span class="sxs-lookup"><span data-stu-id="61a88-270">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="61a88-271">第二項 `ForSession` 測試會判斷 `ForSession` 是否會傳有效工作階段的工作階段構想清單 (`<List<IdeaDTO>>`)。</span><span class="sxs-lookup"><span data-stu-id="61a88-271">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="61a88-272">檢查也會查驗第一個構想，以確認其 `Name` 屬性是否正確：</span><span class="sxs-lookup"><span data-stu-id="61a88-272">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="61a88-273">為了測試 `ModelState` 無效時的 `Create` 方法行為，範例應用程式會將模型錯誤新增至控制器作為測試的一部分。</span><span class="sxs-lookup"><span data-stu-id="61a88-273">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="61a88-274">請勿嘗試在單元測試中測試模型驗證或模型繫結&mdash;只要測試當遇到無效 `ModelState` 時的動作方法行為即可：</span><span class="sxs-lookup"><span data-stu-id="61a88-274">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="61a88-275">第二項 `Create` 測試需要儲存機制傳回 `null`，因此會設定模擬儲存機制傳回 `null`。</span><span class="sxs-lookup"><span data-stu-id="61a88-275">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="61a88-276">不需要建立測試資料庫 (在記憶體中或其他位置)，也不需要建構傳回此結果的查詢。</span><span class="sxs-lookup"><span data-stu-id="61a88-276">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="61a88-277">測試可在單一陳述式中完成，如範例程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="61a88-277">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="61a88-278">第三項 `Create` 測試 (`Create_ReturnsNewlyCreatedIdeaForSession`) 會確認是否呼叫存放庫的 `UpdateAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-278">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="61a88-279">會使用 `Verifiable` 呼叫模擬，再呼叫模擬存放庫的 `Verify` 方法，確認是否已執行可驗證的方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-279">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="61a88-280">單元測試無須負責確保 `UpdateAsync` 方法已儲存資料&mdash;而是透過整合測試來完成。</span><span class="sxs-lookup"><span data-stu-id="61a88-280">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="61a88-281">測試 ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="61a88-281">Test ActionResult\<T></span></span>

<span data-ttu-id="61a88-282">在 ASP.NET Core 2.1 或更新版本中， [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) 可讓您傳回衍生自 `ActionResult` 或傳回特定型別的型別。</span><span class="sxs-lookup"><span data-stu-id="61a88-282">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="61a88-283">範例應用程式包含為指定工作階段 `id` 傳回 `List<IdeaDTO>` 的方法。</span><span class="sxs-lookup"><span data-stu-id="61a88-283">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="61a88-284">如果工作階段 `id` 不存在，則控制器會傳回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>：</span><span class="sxs-lookup"><span data-stu-id="61a88-284">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="61a88-285">`ForSessionActionResult` 控制器的兩項測試會包含於 `ApiIdeasControllerTests`。</span><span class="sxs-lookup"><span data-stu-id="61a88-285">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="61a88-286">第一項測試會確認控制器傳回 `ActionResult`，而不是為不存在之工作階段 `id` 傳回不存在的構想清單：</span><span class="sxs-lookup"><span data-stu-id="61a88-286">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="61a88-287">`ActionResult` 類型為 `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="61a88-287">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="61a88-288"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> 是 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="61a88-288">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="61a88-289">針對有效的工作階段 `id`，第二項測試會確認此方法傳回：</span><span class="sxs-lookup"><span data-stu-id="61a88-289">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="61a88-290">具有 `ActionResult` 類型的 `List<IdeaDTO>`。</span><span class="sxs-lookup"><span data-stu-id="61a88-290">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="61a88-291">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)是一 `List<IdeaDTO>` 種類型。</span><span class="sxs-lookup"><span data-stu-id="61a88-291">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="61a88-292">清單中的第一個項目，是與儲存在模擬工作階段中構想相符的有效構想 (透過呼叫 `GetTestSession` 來取得)。</span><span class="sxs-lookup"><span data-stu-id="61a88-292">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="61a88-293">範例應用程式也包含方法，為指定工作階段建立新的 `Idea`。</span><span class="sxs-lookup"><span data-stu-id="61a88-293">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="61a88-294">控制器會傳回：</span><span class="sxs-lookup"><span data-stu-id="61a88-294">The controller returns:</span></span>

* <span data-ttu-id="61a88-295"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> (針對無效的模型)。</span><span class="sxs-lookup"><span data-stu-id="61a88-295"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="61a88-296"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> (如果工作階段不存在)。</span><span class="sxs-lookup"><span data-stu-id="61a88-296"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="61a88-297"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> (以新構想更新工作階段時)。</span><span class="sxs-lookup"><span data-stu-id="61a88-297"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="61a88-298">`CreateActionResult` 的三項測試都包含在 `ApiIdeasControllerTests` 中。</span><span class="sxs-lookup"><span data-stu-id="61a88-298">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="61a88-299">第一項測試會確認為無效的模型傳回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>。</span><span class="sxs-lookup"><span data-stu-id="61a88-299">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="61a88-300">如果工作階段不存在，第二項測試將檢查是否傳回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>。</span><span class="sxs-lookup"><span data-stu-id="61a88-300">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="61a88-301">針對有效的工作階段 `id`，最終測試會確認：</span><span class="sxs-lookup"><span data-stu-id="61a88-301">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="61a88-302">此方法會以 `BrainstormSession` 類型傳回 `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="61a88-302">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="61a88-303">[ActionResult \<T> 。結果](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)是 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> 。</span><span class="sxs-lookup"><span data-stu-id="61a88-303">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="61a88-304">`CreatedAtActionResult` 類似於具有 `Location` 標頭尸的「201 已建立」回應。</span><span class="sxs-lookup"><span data-stu-id="61a88-304">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="61a88-305">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)是一 `BrainstormSession` 種類型。</span><span class="sxs-lookup"><span data-stu-id="61a88-305">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="61a88-306">已叫用更新工作階段 (`UpdateAsync(testSession)`) 的模擬呼叫。</span><span class="sxs-lookup"><span data-stu-id="61a88-306">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="61a88-307">`Verifiable` 方法呼叫會透過在判斷提示中執行 `mockRepo.Verify()` 來檢查。</span><span class="sxs-lookup"><span data-stu-id="61a88-307">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="61a88-308">會為工作階段傳回兩個 `Idea` 物件。</span><span class="sxs-lookup"><span data-stu-id="61a88-308">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="61a88-309">最後一個項目 (模擬呼叫 `UpdateAsync` 新增的 `Idea`) 會與在測試中新增至工作階段的 `newIdea` 相符。</span><span class="sxs-lookup"><span data-stu-id="61a88-309">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="61a88-310">其他資源</span><span class="sxs-lookup"><span data-stu-id="61a88-310">Additional resources</span></span>

* <xref:test/integration-tests>
* [<span data-ttu-id="61a88-311">使用 Visual Studio 建立和執行單元測試</span><span class="sxs-lookup"><span data-stu-id="61a88-311">Create and run unit tests with Visual Studio</span></span>](/visualstudio/test/unit-test-your-code)
* <span data-ttu-id="61a88-312">[MyTested. AspNetCore. ASP.NET Core mvc 的（Core mvc 的測試程式庫](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc)）：強型別單元測試程式庫，提供用於測試 Mvc 和 web API 應用程式的流暢介面。</span><span class="sxs-lookup"><span data-stu-id="61a88-312">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="61a88-313"> (*不會受到 Microsoft 的維護或支援*) </span><span class="sxs-lookup"><span data-stu-id="61a88-313">(*Not maintained or supported by Microsoft.*)</span></span>
* <span data-ttu-id="61a88-314">[JustMockLite](https://github.com/telerik/JustMockLite)：適用于 .net 開發人員的模擬架構。</span><span class="sxs-lookup"><span data-stu-id="61a88-314">[JustMockLite](https://github.com/telerik/JustMockLite): A mocking framework for .NET developers.</span></span> <span data-ttu-id="61a88-315"> (*不會受到 Microsoft 的維護或支援*) </span><span class="sxs-lookup"><span data-stu-id="61a88-315">(*Not maintained or supported by Microsoft.*)</span></span>

