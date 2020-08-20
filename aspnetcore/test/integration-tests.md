---
title: ASP.NET Core 中的整合測試
author: rick-anderson
description: 了解整合測試如何確保應用程式的元件在基礎結構層級 (包括資料庫、檔案系統和網路) 正確運作。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: b06c06fb5e525a0bdc3df1de50236fa8f76daca9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635108"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="85846-103">ASP.NET Core 中的整合測試</span><span class="sxs-lookup"><span data-stu-id="85846-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="85846-104">[Javier Calvarro Nelson](https://github.com/javiercn)、 [Steve Smith](https://ardalis.com/)和[Jos van der 磚](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="85846-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85846-105">整合測試可確保應用程式的元件在包含應用程式支援基礎結構的層級（例如資料庫、檔案系統和網路）正常運作。</span><span class="sxs-lookup"><span data-stu-id="85846-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="85846-106">ASP.NET Core 使用單元測試架構搭配測試 web 主機和記憶體中的測試伺服器來支援整合測試。</span><span class="sxs-lookup"><span data-stu-id="85846-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="85846-107">本主題假設您對單元測試有基本的瞭解。</span><span class="sxs-lookup"><span data-stu-id="85846-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="85846-108">如果不熟悉測試概念，請參閱 [.Net Core 中的單元測試和 .NET Standard](/dotnet/core/testing/) 主題及其連結的內容。</span><span class="sxs-lookup"><span data-stu-id="85846-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="85846-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="85846-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="85846-110">範例應用程式是 Razor 頁面應用程式，並假設對頁面有基本的瞭解 Razor 。</span><span class="sxs-lookup"><span data-stu-id="85846-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="85846-111">如果不熟悉 Razor 頁面，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="85846-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="85846-112">頁面簡介 Razor</span><span class="sxs-lookup"><span data-stu-id="85846-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="85846-113">開始使用 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="85846-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="85846-114">Razor 頁面單元測試</span><span class="sxs-lookup"><span data-stu-id="85846-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="85846-115">針對測試 Spa，我們建議使用 [Selenium](https://www.seleniumhq.org/)之類的工具，這可以將瀏覽器自動化。</span><span class="sxs-lookup"><span data-stu-id="85846-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="85846-116">整合測試簡介</span><span class="sxs-lookup"><span data-stu-id="85846-116">Introduction to integration tests</span></span>

<span data-ttu-id="85846-117">整合測試會評估應用程式元件在更廣泛的層級上，而不是 [單元測試](/dotnet/core/testing/)。</span><span class="sxs-lookup"><span data-stu-id="85846-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="85846-118">單元測試是用來測試隔離的軟體元件，例如個別的類別方法。</span><span class="sxs-lookup"><span data-stu-id="85846-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="85846-119">整合測試會確認兩個或多個應用程式元件會一起運作，以產生預期的結果，可能包括完整處理要求所需的每個元件。</span><span class="sxs-lookup"><span data-stu-id="85846-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="85846-120">這些廣泛的測試是用來測試應用程式的基礎結構和整個架構，通常包括下列元件：</span><span class="sxs-lookup"><span data-stu-id="85846-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="85846-121">資料庫</span><span class="sxs-lookup"><span data-stu-id="85846-121">Database</span></span>
* <span data-ttu-id="85846-122">檔案系統</span><span class="sxs-lookup"><span data-stu-id="85846-122">File system</span></span>
* <span data-ttu-id="85846-123">網路設備</span><span class="sxs-lookup"><span data-stu-id="85846-123">Network appliances</span></span>
* <span data-ttu-id="85846-124">要求-回應管線</span><span class="sxs-lookup"><span data-stu-id="85846-124">Request-response pipeline</span></span>

<span data-ttu-id="85846-125">單元測試使用製造的元件（稱為 *fakes* 或 *mock 物件*）來取代基礎結構元件。</span><span class="sxs-lookup"><span data-stu-id="85846-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="85846-126">相對於單元測試，整合測試：</span><span class="sxs-lookup"><span data-stu-id="85846-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="85846-127">使用應用程式在生產環境中使用的實際元件。</span><span class="sxs-lookup"><span data-stu-id="85846-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="85846-128">需要更多程式碼和資料處理。</span><span class="sxs-lookup"><span data-stu-id="85846-128">Require more code and data processing.</span></span>
* <span data-ttu-id="85846-129">執行需要較長的時間。</span><span class="sxs-lookup"><span data-stu-id="85846-129">Take longer to run.</span></span>

<span data-ttu-id="85846-130">因此，請將整合測試的使用限制在最重要的基礎結構案例中。</span><span class="sxs-lookup"><span data-stu-id="85846-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="85846-131">如果您可以使用單元測試或整合測試來測試行為，請選擇單元測試。</span><span class="sxs-lookup"><span data-stu-id="85846-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="85846-132">請勿針對每個可能的資料和檔案存取，使用資料庫和檔案系統來撰寫整合測試。</span><span class="sxs-lookup"><span data-stu-id="85846-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="85846-133">無論應用程式之間有多少位置與資料庫和檔案系統互動，一組專注的讀取、寫入、更新和刪除整合測試，通常都能充分測試資料庫和檔案系統元件。</span><span class="sxs-lookup"><span data-stu-id="85846-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="85846-134">針對與這些元件互動的方法邏輯，使用單元測試進行常式測試。</span><span class="sxs-lookup"><span data-stu-id="85846-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="85846-135">在單元測試中，使用基礎結構 fakes/模擬會導致更快速的測試執行。</span><span class="sxs-lookup"><span data-stu-id="85846-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="85846-136">在整合測試的討論中，測試過的專案通常稱為 *受測試的系統*，或簡稱為「SUT」。</span><span class="sxs-lookup"><span data-stu-id="85846-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="85846-137">*本主題中使用 "SUT" 來參考經過測試的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="85846-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="85846-138">ASP.NET Core 整合測試</span><span class="sxs-lookup"><span data-stu-id="85846-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="85846-139">ASP.NET Core 中的整合測試需要下列各項：</span><span class="sxs-lookup"><span data-stu-id="85846-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="85846-140">測試專案可用來包含和執行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="85846-141">測試專案具有此 SUT 的參考。</span><span class="sxs-lookup"><span data-stu-id="85846-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="85846-142">測試專案會建立適用于該 SUT 的測試 web 主機，並使用測試伺服器用戶端來處理與該 SUT 的要求和回應。</span><span class="sxs-lookup"><span data-stu-id="85846-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="85846-143">測試執行器會用來執行測試並報告測試結果。</span><span class="sxs-lookup"><span data-stu-id="85846-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="85846-144">整合測試會遵循一連串的事件，其中包含一般的 *排列*、 *Act*和 *Assert* 測試步驟：</span><span class="sxs-lookup"><span data-stu-id="85846-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="85846-145">已設定 SUT 的 web 主機。</span><span class="sxs-lookup"><span data-stu-id="85846-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="85846-146">建立測試伺服器用戶端以將要求提交給應用程式。</span><span class="sxs-lookup"><span data-stu-id="85846-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="85846-147">執行「 *排列* 測試」步驟：測試應用程式準備要求。</span><span class="sxs-lookup"><span data-stu-id="85846-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="85846-148">執行 *Act* 測試步驟：用戶端會提交要求並接收回應。</span><span class="sxs-lookup"><span data-stu-id="85846-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="85846-149">執行*Assert*測試步驟：*實際*的回應會根據*預期*的回應，驗證為*通過*或*失敗*。</span><span class="sxs-lookup"><span data-stu-id="85846-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="85846-150">此程式會繼續執行，直到執行所有測試為止。</span><span class="sxs-lookup"><span data-stu-id="85846-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="85846-151">系統會報告測試結果。</span><span class="sxs-lookup"><span data-stu-id="85846-151">The test results are reported.</span></span>

<span data-ttu-id="85846-152">測試 web 主機的設定方式通常與測試回合的應用程式一般 web 主機不同。</span><span class="sxs-lookup"><span data-stu-id="85846-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="85846-153">例如，可能會使用不同的資料庫或不同的應用程式設定來進行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="85846-154">基礎結構元件，例如測試 web 主機和記憶體中的測試伺服器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) ，是由 AspNetCore 所提供，或由 [測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝所管理。</span><span class="sxs-lookup"><span data-stu-id="85846-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="85846-155">使用這個封裝可簡化測試的建立和執行。</span><span class="sxs-lookup"><span data-stu-id="85846-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="85846-156">`Microsoft.AspNetCore.Mvc.Testing`封裝會處理下列工作：</span><span class="sxs-lookup"><span data-stu-id="85846-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="85846-157">從 d)  (將相依性檔案從 *.deps*複製到測試專案的*bin*目錄。</span><span class="sxs-lookup"><span data-stu-id="85846-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="85846-158">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為 SUT 的專案根目錄，以便在執行測試時找到靜態檔案和頁面/瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="85846-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="85846-159">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 類別，以簡化使用來啟動載入的工作 `TestServer` 。</span><span class="sxs-lookup"><span data-stu-id="85846-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="85846-160">[單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)檔會描述如何設定測試專案和測試執行器，以及如何執行測試和建議的詳細指示，以瞭解如何命名測試和測試類別。</span><span class="sxs-lookup"><span data-stu-id="85846-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="85846-161">建立應用程式的測試專案時，請將整合測試中的單元測試分成不同的專案。</span><span class="sxs-lookup"><span data-stu-id="85846-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="85846-162">這有助於確保基礎結構測試元件不會不慎包含在單元測試中。</span><span class="sxs-lookup"><span data-stu-id="85846-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="85846-163">單元和整合測試的分隔也可讓您控制要執行的測試集。</span><span class="sxs-lookup"><span data-stu-id="85846-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="85846-164">Razor頁面應用程式和 MVC 應用程式的測試設定幾乎沒有任何差異。</span><span class="sxs-lookup"><span data-stu-id="85846-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="85846-165">唯一的差異在於測試的命名方式。</span><span class="sxs-lookup"><span data-stu-id="85846-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="85846-166">在 Razor 頁面應用程式中，頁面端點的測試通常會在頁面模型類別之後命名 (例如，用 `IndexPageTests` 來測試索引頁面) 的元件整合。</span><span class="sxs-lookup"><span data-stu-id="85846-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="85846-167">在 MVC 應用程式中，通常會依控制器類別來組織測試，並以測試的控制器命名 (例如， `HomeControllerTests` 測試主控制器) 的元件整合。</span><span class="sxs-lookup"><span data-stu-id="85846-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="85846-168">測試應用程式必要條件</span><span class="sxs-lookup"><span data-stu-id="85846-168">Test app prerequisites</span></span>

<span data-ttu-id="85846-169">測試專案必須：</span><span class="sxs-lookup"><span data-stu-id="85846-169">The test project must:</span></span>

* <span data-ttu-id="85846-170">參考 [AspNetCore 的測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝。</span><span class="sxs-lookup"><span data-stu-id="85846-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="85846-171">在專案檔 () 中指定 Web SDK `<Project Sdk="Microsoft.NET.Sdk.Web">` 。</span><span class="sxs-lookup"><span data-stu-id="85846-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="85846-172">您可以在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中看到這些必要條件。</span><span class="sxs-lookup"><span data-stu-id="85846-172">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="85846-173">檢查 *測試/ Razor PagesProject. 測試/ Razor PagesProject* 。</span><span class="sxs-lookup"><span data-stu-id="85846-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="85846-174">範例應用程式會使用 [xUnit](https://xunit.github.io/) 測試架構和 [AngleSharp](https://anglesharp.github.io/) 剖析器程式庫，因此範例應用程式也會參考：</span><span class="sxs-lookup"><span data-stu-id="85846-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="85846-175">xunit</span><span class="sxs-lookup"><span data-stu-id="85846-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="85846-176">xunit。 visualstudio</span><span class="sxs-lookup"><span data-stu-id="85846-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="85846-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="85846-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="85846-178">測試中也會使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="85846-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="85846-179">應用程式參考：</span><span class="sxs-lookup"><span data-stu-id="85846-179">The app references:</span></span>

* [<span data-ttu-id="85846-180">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="85846-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="85846-181">[AspNetCore Identity 。Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="85846-181">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="85846-182">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="85846-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="85846-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="85846-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="85846-184">Microsoft.entityframeworkcore 工具</span><span class="sxs-lookup"><span data-stu-id="85846-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="85846-185">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="85846-185">SUT environment</span></span>

<span data-ttu-id="85846-186">如果未設定 SUT 的 [環境](xref:fundamentals/environments) ，則環境會預設為開發。</span><span class="sxs-lookup"><span data-stu-id="85846-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="85846-187">使用預設 WebApplicationFactory 的基本測試</span><span class="sxs-lookup"><span data-stu-id="85846-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="85846-188">[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用來建立整合測試的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="85846-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="85846-189">`TEntryPoint` 是此 SUT 的進入點類別，通常是 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="85846-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="85846-190">測試類別會將 *類別裝置* 介面 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 來指出類別包含測試，並在類別中的測試之間提供共用物件實例。</span><span class="sxs-lookup"><span data-stu-id="85846-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="85846-191">下列測試類別會 `BasicTests` 使用 `WebApplicationFactory` 來啟動載入並提供測試方法的 [HttpClient](/dotnet/api/system.net.http.httpclient) `Get_EndpointsReturnSuccessAndCorrectContentType` 。</span><span class="sxs-lookup"><span data-stu-id="85846-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="85846-192">方法會檢查回應狀態碼是否成功 (狀態碼在 200-299) 範圍內，且 `Content-Type` 標頭 `text/html; charset=utf-8` 適用于數個應用程式頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="85846-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 會建立的實例 `HttpClient` ，它會自動遵循重新導向和處理 cookie 。</span><span class="sxs-lookup"><span data-stu-id="85846-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="85846-194">依預設，在 cookie 啟用 [GDPR 同意原則](xref:security/gdpr) 時，不會在要求之間保留非必要的。</span><span class="sxs-lookup"><span data-stu-id="85846-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="85846-195">若要保留非必要的 cookie ，例如 TempData 提供者所使用的，請在您的測試中將它們標示為必要。</span><span class="sxs-lookup"><span data-stu-id="85846-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="85846-196">如需將 a 標示 cookie 為必要的指示，請參閱 [重要的 cookie s](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="85846-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="85846-197">自訂 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="85846-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="85846-198">您可以藉由繼承 `WebApplicationFactory` 來建立一或多個自訂處理站，以獨立于測試類別建立 Web 主機設定：</span><span class="sxs-lookup"><span data-stu-id="85846-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="85846-199">繼承 `WebApplicationFactory` 並覆寫 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="85846-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="85846-200">[>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)可讓您使用[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)設定服務集合：</span><span class="sxs-lookup"><span data-stu-id="85846-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="85846-201">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的資料庫植入是由 `InitializeDbForTests` 方法執行。</span><span class="sxs-lookup"><span data-stu-id="85846-201">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="85846-202">[整合測試範例：測試應用程式組織](#test-app-organization)一節中會說明方法。</span><span class="sxs-lookup"><span data-stu-id="85846-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="85846-203">已在其方法中註冊的 SUT 資料庫內容 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="85846-204">測試應用程式的 `builder.ConfigureServices` 回呼會在*after*應用程式的程式 `Startup.ConfigureServices` 代碼執行之後執行。</span><span class="sxs-lookup"><span data-stu-id="85846-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="85846-205">執行順序是 ASP.NET Core 3.0 版本之 [泛型主機](xref:fundamentals/host/generic-host) 的重大變更。</span><span class="sxs-lookup"><span data-stu-id="85846-205">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="85846-206">若要針對測試使用與應用程式資料庫不同的資料庫，則必須在中取代應用程式的資料庫內容 `builder.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-206">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="85846-207">對於仍在使用 [Web 主機](xref:fundamentals/host/web-host)的 SUTs，測試應用程式的 `builder.ConfigureServices` 回呼會在 SUT 的程式碼 *之前* 執行 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-207">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="85846-208">測試應用程式的 `builder.ConfigureTestServices` 回呼會 *在之後*執行。</span><span class="sxs-lookup"><span data-stu-id="85846-208">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="85846-209">範例應用程式會尋找資料庫內容的服務描述項，並使用描述項來移除服務註冊。</span><span class="sxs-lookup"><span data-stu-id="85846-209">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="85846-210">接下來，factory 會加入新 `ApplicationDbContext` 的，其會使用記憶體內部資料庫進行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-210">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="85846-211">若要連接到與記憶體內部資料庫不同的資料庫，請變更將 `UseInMemoryDatabase` 內容連接到不同資料庫的呼叫。</span><span class="sxs-lookup"><span data-stu-id="85846-211">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="85846-212">若要使用 SQL Server 測試資料庫：</span><span class="sxs-lookup"><span data-stu-id="85846-212">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="85846-213">參考專案檔中的[microsoft.entityframeworkcore。](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)</span><span class="sxs-lookup"><span data-stu-id="85846-213">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="85846-214">`UseSqlServer`使用連接字串來呼叫資料庫。</span><span class="sxs-lookup"><span data-stu-id="85846-214">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="85846-215">`CustomWebApplicationFactory`在測試類別中使用自訂。</span><span class="sxs-lookup"><span data-stu-id="85846-215">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="85846-216">下列範例會使用類別中的 factory `IndexPageTests` ：</span><span class="sxs-lookup"><span data-stu-id="85846-216">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="85846-217">範例應用程式的用戶端設定為防止 `HttpClient` 下列重新導向。</span><span class="sxs-lookup"><span data-stu-id="85846-217">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="85846-218">如稍後在 [Mock 驗證](#mock-authentication) 區段中所述，這會允許測試檢查應用程式第一個回應的結果。</span><span class="sxs-lookup"><span data-stu-id="85846-218">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="85846-219">第一個回應是其中許多測試中有標頭的重新導向 `Location` 。</span><span class="sxs-lookup"><span data-stu-id="85846-219">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="85846-220">一般的測試會使用 `HttpClient` 和 helper 方法來處理要求和回應：</span><span class="sxs-lookup"><span data-stu-id="85846-220">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="85846-221">對所有的 SUT 進行 POST 要求都必須滿足應用程式 [資料保護 antiforgery 系統](xref:security/data-protection/introduction)自動進行的 antiforgery 檢查。</span><span class="sxs-lookup"><span data-stu-id="85846-221">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="85846-222">為了排列測試的 POST 要求，測試應用程式必須：</span><span class="sxs-lookup"><span data-stu-id="85846-222">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="85846-223">提出頁面要求。</span><span class="sxs-lookup"><span data-stu-id="85846-223">Make a request for the page.</span></span>
1. <span data-ttu-id="85846-224">cookie從回應中剖析 antiforgery 和要求驗證 token。</span><span class="sxs-lookup"><span data-stu-id="85846-224">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="85846-225">使用 antiforgery cookie 和要求驗證權杖來發出 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="85846-225">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="85846-226">`SendAsync`Helper */) HttpClientExtensions*中的 helper 擴充方法 (協助程式/HtmlHelpers，以及 `GetDocumentAsync` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的協助程式方法 (helper \*/) \* ，請使用[AngleSharp](https://anglesharp.github.io/)剖析器，利用下列方法來處理 antiforgery 檢查：</span><span class="sxs-lookup"><span data-stu-id="85846-226">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="85846-227">`GetDocumentAsync`：接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 並傳回 `IHtmlDocument` 。</span><span class="sxs-lookup"><span data-stu-id="85846-227">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="85846-228">`GetDocumentAsync` 使用可根據原始的來準備 *虛擬回應* 的 factory `HttpResponseMessage` 。</span><span class="sxs-lookup"><span data-stu-id="85846-228">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="85846-229">如需詳細資訊，請參閱 [AngleSharp 檔](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="85846-229">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="85846-230">`SendAsync``HttpClient`撰寫[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)並呼叫 SendAsync 的擴充方法[ (HttpRequestMessage) ](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)將要求提交至 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-230">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="85846-231">`SendAsync`接受 HTML 表單 (`IHtmlFormElement`) 的多載，以及下列各項：</span><span class="sxs-lookup"><span data-stu-id="85846-231">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="85846-232">表單 () 的 [提交] 按鈕 `IHtmlElement`</span><span class="sxs-lookup"><span data-stu-id="85846-232">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="85846-233">表單值集合 (`IEnumerable<KeyValuePair<string, string>>`) </span><span class="sxs-lookup"><span data-stu-id="85846-233">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="85846-234">提交按鈕 (`IHtmlElement`) 和表單值 (`IEnumerable<KeyValuePair<string, string>>`) </span><span class="sxs-lookup"><span data-stu-id="85846-234">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="85846-235">[AngleSharp](https://anglesharp.github.io/) 是協力廠商剖析程式庫，用於本主題和範例應用程式中的示範用途。</span><span class="sxs-lookup"><span data-stu-id="85846-235">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="85846-236">ASP.NET Core 應用程式的整合測試不支援或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="85846-236">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="85846-237">您可以使用其他剖析器，例如 [ (HAP) 的 Html 敏捷套件 ](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="85846-237">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="85846-238">另一種方法是撰寫程式碼來處理 antiforgery 系統的要求驗證權杖，並 cookie 直接 antiforgery。</span><span class="sxs-lookup"><span data-stu-id="85846-238">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="85846-239">使用 WithWebHostBuilder 自訂用戶端</span><span class="sxs-lookup"><span data-stu-id="85846-239">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="85846-240">在測試方法中需要進行其他設定時， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 會建立一個新的， `WebApplicationFactory` 其中包含由設定進一步自訂的 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="85846-240">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="85846-241">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的測試方法會示範的使用方式 `WithWebHostBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="85846-241">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="85846-242">這項測試會在 SUT 中觸發表單提交，在資料庫中執行記錄刪除。</span><span class="sxs-lookup"><span data-stu-id="85846-242">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="85846-243">因為類別中的另一個測試 `IndexPageTests` 會執行刪除資料庫中所有記錄的作業，而且可能會在方法之前執行，所以會 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 在此測試方法中重新植入資料庫，以確保有記錄存在，以便讓 SUT 刪除。</span><span class="sxs-lookup"><span data-stu-id="85846-243">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="85846-244">在 sut 的 `messages` 要求中，會模擬在 sut 中選取表單的第一個 [刪除] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="85846-244">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="85846-245">用戶端選項</span><span class="sxs-lookup"><span data-stu-id="85846-245">Client options</span></span>

<span data-ttu-id="85846-246">下表顯示建立實例時可使用的預設 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="85846-246">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="85846-247">選項</span><span class="sxs-lookup"><span data-stu-id="85846-247">Option</span></span> | <span data-ttu-id="85846-248">描述</span><span class="sxs-lookup"><span data-stu-id="85846-248">Description</span></span> | <span data-ttu-id="85846-249">預設</span><span class="sxs-lookup"><span data-stu-id="85846-249">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="85846-250">Request.allowautoredirect</span><span class="sxs-lookup"><span data-stu-id="85846-250">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="85846-251">取得或設定 `HttpClient` 實例是否應該自動遵循重新導向回應。</span><span class="sxs-lookup"><span data-stu-id="85846-251">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="85846-252">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="85846-252">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="85846-253">取得或設定實例的基底位址 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="85846-253">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| <span data-ttu-id="85846-254">[句 Cookie 柄](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span><span class="sxs-lookup"><span data-stu-id="85846-254">[HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span></span> | <span data-ttu-id="85846-255">取得或設定 `HttpClient` 實例是否應該處理 cookie s。</span><span class="sxs-lookup"><span data-stu-id="85846-255">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="85846-256">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="85846-256">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="85846-257">取得或設定實例應遵循的重新導向回應數目上限 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="85846-257">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="85846-258">7</span><span class="sxs-lookup"><span data-stu-id="85846-258">7</span></span> |

<span data-ttu-id="85846-259">建立 `WebApplicationFactoryClientOptions` 類別，並將它傳遞給 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (預設值會顯示在程式碼範例) 中：</span><span class="sxs-lookup"><span data-stu-id="85846-259">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="85846-260">插入 mock 服務</span><span class="sxs-lookup"><span data-stu-id="85846-260">Inject mock services</span></span>

<span data-ttu-id="85846-261">您可以在主機建立器上呼叫 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，以在測試中覆寫服務。</span><span class="sxs-lookup"><span data-stu-id="85846-261">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="85846-262">**若要插入模擬服務，則必須具有 `Startup` 具有方法的類別 `Startup.ConfigureServices` 。**</span><span class="sxs-lookup"><span data-stu-id="85846-262">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="85846-263">範例 SUT 包含會傳回報價的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="85846-263">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="85846-264">當要求索引頁面時，引號會內嵌在索引頁面的隱藏欄位中。</span><span class="sxs-lookup"><span data-stu-id="85846-264">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="85846-265">*Services/IQuoteService .cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-265">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="85846-266">*Services/QuoteService .cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-266">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="85846-267">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-267">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="85846-268">*Pages/Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-268">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="85846-269">*Pages/Index .cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-269">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="85846-270">執行 SUT 應用程式時，會產生下列標記：</span><span class="sxs-lookup"><span data-stu-id="85846-270">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="85846-271">若要在整合測試中測試服務和報價插入，模擬服務會由測試插入至 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-271">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="85846-272">模擬服務會將應用程式取代為 `QuoteService` 測試應用程式所提供的服務，稱為 `TestQuoteService` ：</span><span class="sxs-lookup"><span data-stu-id="85846-272">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="85846-273">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-273">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="85846-274">`ConfigureTestServices` 會呼叫，且已註冊範圍服務：</span><span class="sxs-lookup"><span data-stu-id="85846-274">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="85846-275">測試執行期間所產生的標記會反映所提供的報價文字 `TestQuoteService` ，因此判斷提示會通過：</span><span class="sxs-lookup"><span data-stu-id="85846-275">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="85846-276">Mock 驗證</span><span class="sxs-lookup"><span data-stu-id="85846-276">Mock authentication</span></span>

<span data-ttu-id="85846-277">類別中的測試 `AuthTests` 會檢查安全的端點：</span><span class="sxs-lookup"><span data-stu-id="85846-277">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="85846-278">將未經驗證的使用者重新導向至應用程式的登入頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-278">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="85846-279">傳回已驗證使用者的內容。</span><span class="sxs-lookup"><span data-stu-id="85846-279">Returns content for an authenticated user.</span></span>

<span data-ttu-id="85846-280">在 SUT 中， `/SecurePage` 頁面會使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 慣例，將 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 套用至頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-280">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="85846-281">如需詳細資訊，請參閱[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="85846-281">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="85846-282">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 測試中， [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 會設定為 [不允許重新導向]，方法是將 [request.allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 設定為 `false` ：</span><span class="sxs-lookup"><span data-stu-id="85846-282">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="85846-283">藉由不允許用戶端遵循重新導向，可進行下列檢查：</span><span class="sxs-lookup"><span data-stu-id="85846-283">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="85846-284">在重新導向至登入頁面之後，您可以根據預期的 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode) 來檢查您所傳回的狀態碼，而不是最後的狀態碼，這會是 [HttpStatusCode。確定](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="85846-284">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="85846-285">`Location`檢查回應標頭中的標頭值，以確認它的開頭 `http://localhost/Identity/Account/Login` 不是最後一個登入頁面回應，而且 `Location` 標頭不會出現。</span><span class="sxs-lookup"><span data-stu-id="85846-285">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="85846-286">測試應用程式可以在 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模擬，以便測試驗證和授權的層面。</span><span class="sxs-lookup"><span data-stu-id="85846-286">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="85846-287">基本案例會傳回 [AuthenticateResult。 Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="85846-287">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="85846-288">`TestAuthHandler`當驗證配置設定為的註冊位置時，會呼叫來驗證 `Test` 使用者 `AddAuthentication` `ConfigureTestServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-288">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`.</span></span> <span data-ttu-id="85846-289">`Test`配置必須符合您的應用程式所預期的配置是很重要的。</span><span class="sxs-lookup"><span data-stu-id="85846-289">It's important for the `Test` scheme to match the scheme your app expects.</span></span> <span data-ttu-id="85846-290">否則，驗證將無法運作。</span><span class="sxs-lookup"><span data-stu-id="85846-290">Otherwise, authentication won't work.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="85846-291">如需的詳細資訊 `WebApplicationFactoryClientOptions` ，請參閱 [用戶端選項](#client-options) 一節。</span><span class="sxs-lookup"><span data-stu-id="85846-291">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="85846-292">設定環境</span><span class="sxs-lookup"><span data-stu-id="85846-292">Set the environment</span></span>

<span data-ttu-id="85846-293">根據預設，已將 SUT 的主機和應用程式環境設定為使用開發環境。</span><span class="sxs-lookup"><span data-stu-id="85846-293">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="85846-294">若要在使用時覆寫 SUT 的環境 `IHostBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="85846-294">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="85846-295">設定 `ASPNETCORE_ENVIRONMENT` 環境變數 (例如，、 `Staging` `Production` 或其他自訂值，例如 `Testing`) 。</span><span class="sxs-lookup"><span data-stu-id="85846-295">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="85846-296">`CreateHostBuilder`在測試應用程式中覆寫，以讀取前面加上的環境變數 `ASPNETCORE` 。</span><span class="sxs-lookup"><span data-stu-id="85846-296">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="85846-297">如果 SUT 使用 () 的 Web 主機 `IWebHostBuilder` ，請覆寫 `CreateWebHostBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="85846-297">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="85846-298">測試基礎結構如何推斷應用程式內容根路徑</span><span class="sxs-lookup"><span data-stu-id="85846-298">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="85846-299">此函式會藉 `WebApplicationFactory` 由在包含整合測試與元件相等的元件上搜尋[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) ，來推斷應用程式[內容的根](xref:fundamentals/index#content-root)路徑 `TEntryPoint` `System.Reflection.Assembly.FullName` 。</span><span class="sxs-lookup"><span data-stu-id="85846-299">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="85846-300">如果找不到具有正確索引鍵的屬性，則會 `WebApplicationFactory` 切換回以搜尋方案檔 (*.Sln*) 並將 `TEntryPoint` 元件名稱附加至方案目錄。</span><span class="sxs-lookup"><span data-stu-id="85846-300">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="85846-301">應用程式根目錄 (內容根路徑) 用來探索視圖和內容檔案。</span><span class="sxs-lookup"><span data-stu-id="85846-301">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="85846-302">停用陰影複製</span><span class="sxs-lookup"><span data-stu-id="85846-302">Disable shadow copying</span></span>

<span data-ttu-id="85846-303">陰影複製會導致測試在和輸出目錄不同的目錄中執行。</span><span class="sxs-lookup"><span data-stu-id="85846-303">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="85846-304">若要讓測試正常運作，必須停用陰影複製。</span><span class="sxs-lookup"><span data-stu-id="85846-304">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="85846-305">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)會使用 xUnit 並停用 xUnit 的陰影複製，方法是在具有正確設定的檔案*上包含xunit.runner.js* 。</span><span class="sxs-lookup"><span data-stu-id="85846-305">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="85846-306">如需詳細資訊，請參閱 [使用 JSON 設定 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="85846-306">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="85846-307">使用下列內容，將檔案 \* 上的xunit.runner.js\* 新增至測試專案的根目錄：</span><span class="sxs-lookup"><span data-stu-id="85846-307">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="85846-308">物件的處置</span><span class="sxs-lookup"><span data-stu-id="85846-308">Disposal of objects</span></span>

<span data-ttu-id="85846-309">`IClassFixture`執行實施測試之後，當 xUnit 處置[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)時，會處置[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="85846-309">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="85846-310">如果開發人員所具現化的物件需要處置，請在執行時處置它們 `IClassFixture` 。</span><span class="sxs-lookup"><span data-stu-id="85846-310">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="85846-311">如需詳細資訊，請參閱 [執行 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="85846-311">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="85846-312">整合測試範例</span><span class="sxs-lookup"><span data-stu-id="85846-312">Integration tests sample</span></span>

<span data-ttu-id="85846-313">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)是由兩個應用程式所組成：</span><span class="sxs-lookup"><span data-stu-id="85846-313">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="85846-314">應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-314">App</span></span> | <span data-ttu-id="85846-315">專案目錄</span><span class="sxs-lookup"><span data-stu-id="85846-315">Project directory</span></span> | <span data-ttu-id="85846-316">描述</span><span class="sxs-lookup"><span data-stu-id="85846-316">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="85846-317"> (在 SUT) 的訊息應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-317">Message app (the SUT)</span></span> | <span data-ttu-id="85846-318">*src/ Razor PagesProject*</span><span class="sxs-lookup"><span data-stu-id="85846-318">*src/RazorPagesProject*</span></span> | <span data-ttu-id="85846-319">允許使用者新增、刪除、刪除所有訊息，以及分析訊息。</span><span class="sxs-lookup"><span data-stu-id="85846-319">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="85846-320">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-320">Test app</span></span> | <span data-ttu-id="85846-321">*測試/ Razor PagesProject*</span><span class="sxs-lookup"><span data-stu-id="85846-321">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="85846-322">用於整合測試的 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-322">Used to integration test the SUT.</span></span> |

<span data-ttu-id="85846-323">您可以使用 IDE 的內建測試功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）來執行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-323">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="85846-324">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesProject* ] 目錄中的命令提示字元執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="85846-324">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="85846-325"> (的 SUT) 組織的訊息應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-325">Message app (SUT) organization</span></span>

<span data-ttu-id="85846-326">SUT 是 Razor 具有下列特性的頁面訊息系統：</span><span class="sxs-lookup"><span data-stu-id="85846-326">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="85846-327">應用程式的 [索引] 頁面 (*pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法，可控制訊息的新增、刪除和分析 (每個訊息) 的平均單字。</span><span class="sxs-lookup"><span data-stu-id="85846-327">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="85846-328">訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。</span><span class="sxs-lookup"><span data-stu-id="85846-328">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="85846-329">`Text`屬性是必要的，且限制為200個字元。</span><span class="sxs-lookup"><span data-stu-id="85846-329">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="85846-330">訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/)&#8224; 來儲存。</span><span class="sxs-lookup"><span data-stu-id="85846-330">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="85846-331">應用程式在其資料庫內容類別中包含 (DAL) 的資料存取層， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。</span><span class="sxs-lookup"><span data-stu-id="85846-331">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="85846-332">如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。</span><span class="sxs-lookup"><span data-stu-id="85846-332">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="85846-333">應用程式包含 `/SecurePage` 只能由已驗證的使用者存取的。</span><span class="sxs-lookup"><span data-stu-id="85846-333">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="85846-334">&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-334">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="85846-335">本主題使用 [xUnit](https://xunit.github.io/) 測試架構。</span><span class="sxs-lookup"><span data-stu-id="85846-335">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="85846-336">跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="85846-336">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="85846-337">雖然應用程式不會使用存放庫模式，而不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。</span><span class="sxs-lookup"><span data-stu-id="85846-337">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="85846-338">如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 [測試控制器邏輯](/aspnet/core/mvc/controllers/testing) (範例會) 執行儲存機制模式。</span><span class="sxs-lookup"><span data-stu-id="85846-338">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="85846-339">測試應用程式組織</span><span class="sxs-lookup"><span data-stu-id="85846-339">Test app organization</span></span>

<span data-ttu-id="85846-340">測試應用程式是 [ *測試/ Razor PagesProject* ] 目錄中的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="85846-340">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="85846-341">測試應用程式目錄</span><span class="sxs-lookup"><span data-stu-id="85846-341">Test app directory</span></span> | <span data-ttu-id="85846-342">描述</span><span class="sxs-lookup"><span data-stu-id="85846-342">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="85846-343">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="85846-343">*AuthTests*</span></span> | <span data-ttu-id="85846-344">包含的測試方法：</span><span class="sxs-lookup"><span data-stu-id="85846-344">Contains test methods for:</span></span><ul><li><span data-ttu-id="85846-345">未經驗證的使用者存取安全的頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-345">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="85846-346">使用模擬以已驗證的使用者存取安全的頁面 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> 。</span><span class="sxs-lookup"><span data-stu-id="85846-346">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="85846-347">取得 GitHub 使用者設定檔，並檢查設定檔的使用者登入。</span><span class="sxs-lookup"><span data-stu-id="85846-347">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="85846-348">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="85846-348">*BasicTests*</span></span> | <span data-ttu-id="85846-349">包含路由和內容類型的測試方法。</span><span class="sxs-lookup"><span data-stu-id="85846-349">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="85846-350">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="85846-350">*IntegrationTests*</span></span> | <span data-ttu-id="85846-351">包含使用自訂類別之 [索引] 頁面的整合測試 `WebApplicationFactory` 。</span><span class="sxs-lookup"><span data-stu-id="85846-351">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="85846-352">*協助程式/公用程式*</span><span class="sxs-lookup"><span data-stu-id="85846-352">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="85846-353">*Utilities.cs* 包含 `InitializeDbForTests` 用來將測試資料植入資料庫的方法。</span><span class="sxs-lookup"><span data-stu-id="85846-353">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="85846-354">*HtmlHelpers.cs* 會提供方法，以傳回 `IHtmlDocument` 測試方法所使用的 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="85846-354">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="85846-355">*HttpClientExtensions.cs* 會提供的多載， `SendAsync` 以將要求提交至 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-355">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="85846-356">測試架構為 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="85846-356">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="85846-357">整合測試是使用 [AspNetCore TestHost](/dotnet/api/microsoft.aspnetcore.testhost)，其中包含 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="85846-357">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="85846-358">由於使用 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝來設定測試主機和測試伺服器，因此 `TestHost` 和套件不需要測試應用程式的專案檔 `TestServer` 或開發人員在測試應用程式中進行直接封裝參考。</span><span class="sxs-lookup"><span data-stu-id="85846-358">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="85846-359">**植入資料庫以進行測試**</span><span class="sxs-lookup"><span data-stu-id="85846-359">**Seeding the database for testing**</span></span>

<span data-ttu-id="85846-360">在測試執行之前，整合測試通常需要資料庫中的小型資料集。</span><span class="sxs-lookup"><span data-stu-id="85846-360">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="85846-361">例如，刪除測試會呼叫資料庫記錄刪除，因此資料庫必須至少有一筆記錄，刪除要求才能成功。</span><span class="sxs-lookup"><span data-stu-id="85846-361">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="85846-362">範例應用程式會使用 *Utilities.cs* 中的三個訊息來植入資料庫，測試可在執行時使用這些訊息：</span><span class="sxs-lookup"><span data-stu-id="85846-362">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="85846-363">已在其方法中註冊的 SUT 資料庫內容 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-363">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="85846-364">測試應用程式的 `builder.ConfigureServices` 回呼會在*after*應用程式的程式 `Startup.ConfigureServices` 代碼執行之後執行。</span><span class="sxs-lookup"><span data-stu-id="85846-364">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="85846-365">若要使用不同的資料庫進行測試，必須在中取代應用程式的資料庫內容 `builder.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-365">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="85846-366">如需詳細資訊，請參閱 [自訂 WebApplicationFactory](#customize-webapplicationfactory) 一節。</span><span class="sxs-lookup"><span data-stu-id="85846-366">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="85846-367">對於仍在使用 [Web 主機](xref:fundamentals/host/web-host)的 SUTs，測試應用程式的 `builder.ConfigureServices` 回呼會在 SUT 的程式碼 *之前* 執行 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-367">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="85846-368">測試應用程式的 `builder.ConfigureTestServices` 回呼會 *在之後*執行。</span><span class="sxs-lookup"><span data-stu-id="85846-368">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85846-369">整合測試可確保應用程式的元件在包含應用程式支援基礎結構的層級（例如資料庫、檔案系統和網路）正常運作。</span><span class="sxs-lookup"><span data-stu-id="85846-369">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="85846-370">ASP.NET Core 使用單元測試架構搭配測試 web 主機和記憶體中的測試伺服器來支援整合測試。</span><span class="sxs-lookup"><span data-stu-id="85846-370">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="85846-371">本主題假設您對單元測試有基本的瞭解。</span><span class="sxs-lookup"><span data-stu-id="85846-371">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="85846-372">如果不熟悉測試概念，請參閱 [.Net Core 中的單元測試和 .NET Standard](/dotnet/core/testing/) 主題及其連結的內容。</span><span class="sxs-lookup"><span data-stu-id="85846-372">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="85846-373">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="85846-373">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="85846-374">範例應用程式是 Razor 頁面應用程式，並假設對頁面有基本的瞭解 Razor 。</span><span class="sxs-lookup"><span data-stu-id="85846-374">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="85846-375">如果不熟悉 Razor 頁面，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="85846-375">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="85846-376">頁面簡介 Razor</span><span class="sxs-lookup"><span data-stu-id="85846-376">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="85846-377">開始使用 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="85846-377">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="85846-378">Razor 頁面單元測試</span><span class="sxs-lookup"><span data-stu-id="85846-378">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="85846-379">針對測試 Spa，我們建議使用 [Selenium](https://www.seleniumhq.org/)之類的工具，這可以將瀏覽器自動化。</span><span class="sxs-lookup"><span data-stu-id="85846-379">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="85846-380">整合測試簡介</span><span class="sxs-lookup"><span data-stu-id="85846-380">Introduction to integration tests</span></span>

<span data-ttu-id="85846-381">整合測試會評估應用程式元件在更廣泛的層級上，而不是 [單元測試](/dotnet/core/testing/)。</span><span class="sxs-lookup"><span data-stu-id="85846-381">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="85846-382">單元測試是用來測試隔離的軟體元件，例如個別的類別方法。</span><span class="sxs-lookup"><span data-stu-id="85846-382">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="85846-383">整合測試會確認兩個或多個應用程式元件會一起運作，以產生預期的結果，可能包括完整處理要求所需的每個元件。</span><span class="sxs-lookup"><span data-stu-id="85846-383">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="85846-384">這些廣泛的測試是用來測試應用程式的基礎結構和整個架構，通常包括下列元件：</span><span class="sxs-lookup"><span data-stu-id="85846-384">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="85846-385">資料庫</span><span class="sxs-lookup"><span data-stu-id="85846-385">Database</span></span>
* <span data-ttu-id="85846-386">檔案系統</span><span class="sxs-lookup"><span data-stu-id="85846-386">File system</span></span>
* <span data-ttu-id="85846-387">網路設備</span><span class="sxs-lookup"><span data-stu-id="85846-387">Network appliances</span></span>
* <span data-ttu-id="85846-388">要求-回應管線</span><span class="sxs-lookup"><span data-stu-id="85846-388">Request-response pipeline</span></span>

<span data-ttu-id="85846-389">單元測試使用製造的元件（稱為 *fakes* 或 *mock 物件*）來取代基礎結構元件。</span><span class="sxs-lookup"><span data-stu-id="85846-389">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="85846-390">相對於單元測試，整合測試：</span><span class="sxs-lookup"><span data-stu-id="85846-390">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="85846-391">使用應用程式在生產環境中使用的實際元件。</span><span class="sxs-lookup"><span data-stu-id="85846-391">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="85846-392">需要更多程式碼和資料處理。</span><span class="sxs-lookup"><span data-stu-id="85846-392">Require more code and data processing.</span></span>
* <span data-ttu-id="85846-393">執行需要較長的時間。</span><span class="sxs-lookup"><span data-stu-id="85846-393">Take longer to run.</span></span>

<span data-ttu-id="85846-394">因此，請將整合測試的使用限制在最重要的基礎結構案例中。</span><span class="sxs-lookup"><span data-stu-id="85846-394">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="85846-395">如果您可以使用單元測試或整合測試來測試行為，請選擇單元測試。</span><span class="sxs-lookup"><span data-stu-id="85846-395">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="85846-396">請勿針對每個可能的資料和檔案存取，使用資料庫和檔案系統來撰寫整合測試。</span><span class="sxs-lookup"><span data-stu-id="85846-396">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="85846-397">無論應用程式之間有多少位置與資料庫和檔案系統互動，一組專注的讀取、寫入、更新和刪除整合測試，通常都能充分測試資料庫和檔案系統元件。</span><span class="sxs-lookup"><span data-stu-id="85846-397">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="85846-398">針對與這些元件互動的方法邏輯，使用單元測試進行常式測試。</span><span class="sxs-lookup"><span data-stu-id="85846-398">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="85846-399">在單元測試中，使用基礎結構 fakes/模擬會導致更快速的測試執行。</span><span class="sxs-lookup"><span data-stu-id="85846-399">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="85846-400">在整合測試的討論中，測試過的專案通常稱為 *受測試的系統*，或簡稱為「SUT」。</span><span class="sxs-lookup"><span data-stu-id="85846-400">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="85846-401">*本主題中使用 "SUT" 來參考經過測試的 ASP.NET Core 應用程式。*</span><span class="sxs-lookup"><span data-stu-id="85846-401">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="85846-402">ASP.NET Core 整合測試</span><span class="sxs-lookup"><span data-stu-id="85846-402">ASP.NET Core integration tests</span></span>

<span data-ttu-id="85846-403">ASP.NET Core 中的整合測試需要下列各項：</span><span class="sxs-lookup"><span data-stu-id="85846-403">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="85846-404">測試專案可用來包含和執行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-404">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="85846-405">測試專案具有此 SUT 的參考。</span><span class="sxs-lookup"><span data-stu-id="85846-405">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="85846-406">測試專案會建立適用于該 SUT 的測試 web 主機，並使用測試伺服器用戶端來處理與該 SUT 的要求和回應。</span><span class="sxs-lookup"><span data-stu-id="85846-406">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="85846-407">測試執行器會用來執行測試並報告測試結果。</span><span class="sxs-lookup"><span data-stu-id="85846-407">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="85846-408">整合測試會遵循一連串的事件，其中包含一般的 *排列*、 *Act*和 *Assert* 測試步驟：</span><span class="sxs-lookup"><span data-stu-id="85846-408">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="85846-409">已設定 SUT 的 web 主機。</span><span class="sxs-lookup"><span data-stu-id="85846-409">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="85846-410">建立測試伺服器用戶端以將要求提交給應用程式。</span><span class="sxs-lookup"><span data-stu-id="85846-410">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="85846-411">執行「 *排列* 測試」步驟：測試應用程式準備要求。</span><span class="sxs-lookup"><span data-stu-id="85846-411">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="85846-412">執行 *Act* 測試步驟：用戶端會提交要求並接收回應。</span><span class="sxs-lookup"><span data-stu-id="85846-412">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="85846-413">執行*Assert*測試步驟：*實際*的回應會根據*預期*的回應，驗證為*通過*或*失敗*。</span><span class="sxs-lookup"><span data-stu-id="85846-413">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="85846-414">此程式會繼續執行，直到執行所有測試為止。</span><span class="sxs-lookup"><span data-stu-id="85846-414">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="85846-415">系統會報告測試結果。</span><span class="sxs-lookup"><span data-stu-id="85846-415">The test results are reported.</span></span>

<span data-ttu-id="85846-416">測試 web 主機的設定方式通常與測試回合的應用程式一般 web 主機不同。</span><span class="sxs-lookup"><span data-stu-id="85846-416">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="85846-417">例如，可能會使用不同的資料庫或不同的應用程式設定來進行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-417">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="85846-418">基礎結構元件，例如測試 web 主機和記憶體中的測試伺服器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) ，是由 AspNetCore 所提供，或由 [測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝所管理。</span><span class="sxs-lookup"><span data-stu-id="85846-418">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="85846-419">使用這個封裝可簡化測試的建立和執行。</span><span class="sxs-lookup"><span data-stu-id="85846-419">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="85846-420">`Microsoft.AspNetCore.Mvc.Testing`封裝會處理下列工作：</span><span class="sxs-lookup"><span data-stu-id="85846-420">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="85846-421">從 d)  (將相依性檔案從 *.deps*複製到測試專案的*bin*目錄。</span><span class="sxs-lookup"><span data-stu-id="85846-421">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="85846-422">將 [內容根目錄](xref:fundamentals/index#content-root) 設定為 SUT 的專案根目錄，以便在執行測試時找到靜態檔案和頁面/瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="85846-422">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="85846-423">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 類別，以簡化使用來啟動載入的工作 `TestServer` 。</span><span class="sxs-lookup"><span data-stu-id="85846-423">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="85846-424">[單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)檔會描述如何設定測試專案和測試執行器，以及如何執行測試和建議的詳細指示，以瞭解如何命名測試和測試類別。</span><span class="sxs-lookup"><span data-stu-id="85846-424">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="85846-425">建立應用程式的測試專案時，請將整合測試中的單元測試分成不同的專案。</span><span class="sxs-lookup"><span data-stu-id="85846-425">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="85846-426">這有助於確保基礎結構測試元件不會不慎包含在單元測試中。</span><span class="sxs-lookup"><span data-stu-id="85846-426">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="85846-427">單元和整合測試的分隔也可讓您控制要執行的測試集。</span><span class="sxs-lookup"><span data-stu-id="85846-427">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="85846-428">Razor頁面應用程式和 MVC 應用程式的測試設定幾乎沒有任何差異。</span><span class="sxs-lookup"><span data-stu-id="85846-428">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="85846-429">唯一的差異在於測試的命名方式。</span><span class="sxs-lookup"><span data-stu-id="85846-429">The only difference is in how the tests are named.</span></span> <span data-ttu-id="85846-430">在 Razor 頁面應用程式中，頁面端點的測試通常會在頁面模型類別之後命名 (例如，用 `IndexPageTests` 來測試索引頁面) 的元件整合。</span><span class="sxs-lookup"><span data-stu-id="85846-430">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="85846-431">在 MVC 應用程式中，通常會依控制器類別來組織測試，並以測試的控制器命名 (例如， `HomeControllerTests` 測試主控制器) 的元件整合。</span><span class="sxs-lookup"><span data-stu-id="85846-431">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="85846-432">測試應用程式必要條件</span><span class="sxs-lookup"><span data-stu-id="85846-432">Test app prerequisites</span></span>

<span data-ttu-id="85846-433">測試專案必須：</span><span class="sxs-lookup"><span data-stu-id="85846-433">The test project must:</span></span>

* <span data-ttu-id="85846-434">參考下列套件：</span><span class="sxs-lookup"><span data-stu-id="85846-434">Reference the following packages:</span></span>
  * [<span data-ttu-id="85846-435">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="85846-435">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="85846-436">AspNetCore 進行測試</span><span class="sxs-lookup"><span data-stu-id="85846-436">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="85846-437">在專案檔 () 中指定 Web SDK `<Project Sdk="Microsoft.NET.Sdk.Web">` 。</span><span class="sxs-lookup"><span data-stu-id="85846-437">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="85846-438">參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)時，需要 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="85846-438">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="85846-439">您可以在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中看到這些必要條件。</span><span class="sxs-lookup"><span data-stu-id="85846-439">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="85846-440">檢查 *測試/ Razor PagesProject. 測試/ Razor PagesProject* 。</span><span class="sxs-lookup"><span data-stu-id="85846-440">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="85846-441">範例應用程式會使用 [xUnit](https://xunit.github.io/) 測試架構和 [AngleSharp](https://anglesharp.github.io/) 剖析器程式庫，因此範例應用程式也會參考：</span><span class="sxs-lookup"><span data-stu-id="85846-441">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="85846-442">xunit</span><span class="sxs-lookup"><span data-stu-id="85846-442">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="85846-443">xunit。 visualstudio</span><span class="sxs-lookup"><span data-stu-id="85846-443">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="85846-444">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="85846-444">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="85846-445">SUT 環境</span><span class="sxs-lookup"><span data-stu-id="85846-445">SUT environment</span></span>

<span data-ttu-id="85846-446">如果未設定 SUT 的 [環境](xref:fundamentals/environments) ，則環境會預設為開發。</span><span class="sxs-lookup"><span data-stu-id="85846-446">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="85846-447">使用預設 WebApplicationFactory 的基本測試</span><span class="sxs-lookup"><span data-stu-id="85846-447">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="85846-448">[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用來建立整合測試的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="85846-448">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="85846-449">`TEntryPoint` 是此 SUT 的進入點類別，通常是 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="85846-449">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="85846-450">測試類別會將 *類別裝置* 介面 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 來指出類別包含測試，並在類別中的測試之間提供共用物件實例。</span><span class="sxs-lookup"><span data-stu-id="85846-450">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="85846-451">下列測試類別會 `BasicTests` 使用 `WebApplicationFactory` 來啟動載入並提供測試方法的 [HttpClient](/dotnet/api/system.net.http.httpclient) `Get_EndpointsReturnSuccessAndCorrectContentType` 。</span><span class="sxs-lookup"><span data-stu-id="85846-451">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="85846-452">方法會檢查回應狀態碼是否成功 (狀態碼在 200-299) 範圍內，且 `Content-Type` 標頭 `text/html; charset=utf-8` 適用于數個應用程式頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-452">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="85846-453">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 會建立的實例 `HttpClient` ，它會自動遵循重新導向和處理 cookie 。</span><span class="sxs-lookup"><span data-stu-id="85846-453">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="85846-454">依預設，在 cookie 啟用 [GDPR 同意原則](xref:security/gdpr) 時，不會在要求之間保留非必要的。</span><span class="sxs-lookup"><span data-stu-id="85846-454">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="85846-455">若要保留非必要的 cookie ，例如 TempData 提供者所使用的，請在您的測試中將它們標示為必要。</span><span class="sxs-lookup"><span data-stu-id="85846-455">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="85846-456">如需將 a 標示 cookie 為必要的指示，請參閱 [重要的 cookie s](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="85846-456">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="85846-457">自訂 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="85846-457">Customize WebApplicationFactory</span></span>

<span data-ttu-id="85846-458">您可以藉由繼承 `WebApplicationFactory` 來建立一或多個自訂處理站，以獨立于測試類別建立 Web 主機設定：</span><span class="sxs-lookup"><span data-stu-id="85846-458">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="85846-459">繼承 `WebApplicationFactory` 並覆寫 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="85846-459">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="85846-460">[>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)可讓您使用[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)設定服務集合，該集合會在應用程式之前執行 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="85846-460">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85846-461">[>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)可讓您使用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)覆寫和修改服務集合中的已註冊服務：</span><span class="sxs-lookup"><span data-stu-id="85846-461">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="85846-462">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的資料庫植入是由 `InitializeDbForTests` 方法執行。</span><span class="sxs-lookup"><span data-stu-id="85846-462">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="85846-463">[整合測試範例：測試應用程式組織](#test-app-organization)一節中會說明方法。</span><span class="sxs-lookup"><span data-stu-id="85846-463">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="85846-464">`CustomWebApplicationFactory`在測試類別中使用自訂。</span><span class="sxs-lookup"><span data-stu-id="85846-464">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="85846-465">下列範例會使用類別中的 factory `IndexPageTests` ：</span><span class="sxs-lookup"><span data-stu-id="85846-465">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="85846-466">範例應用程式的用戶端設定為防止 `HttpClient` 下列重新導向。</span><span class="sxs-lookup"><span data-stu-id="85846-466">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="85846-467">如稍後在 [Mock 驗證](#mock-authentication) 區段中所述，這會允許測試檢查應用程式第一個回應的結果。</span><span class="sxs-lookup"><span data-stu-id="85846-467">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="85846-468">第一個回應是其中許多測試中有標頭的重新導向 `Location` 。</span><span class="sxs-lookup"><span data-stu-id="85846-468">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="85846-469">一般的測試會使用 `HttpClient` 和 helper 方法來處理要求和回應：</span><span class="sxs-lookup"><span data-stu-id="85846-469">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="85846-470">對所有的 SUT 進行 POST 要求都必須滿足應用程式 [資料保護 antiforgery 系統](xref:security/data-protection/introduction)自動進行的 antiforgery 檢查。</span><span class="sxs-lookup"><span data-stu-id="85846-470">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="85846-471">為了排列測試的 POST 要求，測試應用程式必須：</span><span class="sxs-lookup"><span data-stu-id="85846-471">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="85846-472">提出頁面要求。</span><span class="sxs-lookup"><span data-stu-id="85846-472">Make a request for the page.</span></span>
1. <span data-ttu-id="85846-473">cookie從回應中剖析 antiforgery 和要求驗證 token。</span><span class="sxs-lookup"><span data-stu-id="85846-473">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="85846-474">使用 antiforgery cookie 和要求驗證權杖來發出 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="85846-474">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="85846-475">`SendAsync`Helper */) HttpClientExtensions*中的 helper 擴充方法 (協助程式/HtmlHelpers，以及 `GetDocumentAsync` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的協助程式方法 (helper \*/) \* ，請使用[AngleSharp](https://anglesharp.github.io/)剖析器，利用下列方法來處理 antiforgery 檢查：</span><span class="sxs-lookup"><span data-stu-id="85846-475">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="85846-476">`GetDocumentAsync`：接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 並傳回 `IHtmlDocument` 。</span><span class="sxs-lookup"><span data-stu-id="85846-476">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="85846-477">`GetDocumentAsync` 使用可根據原始的來準備 *虛擬回應* 的 factory `HttpResponseMessage` 。</span><span class="sxs-lookup"><span data-stu-id="85846-477">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="85846-478">如需詳細資訊，請參閱 [AngleSharp 檔](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="85846-478">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="85846-479">`SendAsync``HttpClient`撰寫[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)並呼叫 SendAsync 的擴充方法[ (HttpRequestMessage) ](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)將要求提交至 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-479">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="85846-480">`SendAsync`接受 HTML 表單 (`IHtmlFormElement`) 的多載，以及下列各項：</span><span class="sxs-lookup"><span data-stu-id="85846-480">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="85846-481">表單 () 的 [提交] 按鈕 `IHtmlElement`</span><span class="sxs-lookup"><span data-stu-id="85846-481">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="85846-482">表單值集合 (`IEnumerable<KeyValuePair<string, string>>`) </span><span class="sxs-lookup"><span data-stu-id="85846-482">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="85846-483">提交按鈕 (`IHtmlElement`) 和表單值 (`IEnumerable<KeyValuePair<string, string>>`) </span><span class="sxs-lookup"><span data-stu-id="85846-483">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="85846-484">[AngleSharp](https://anglesharp.github.io/) 是協力廠商剖析程式庫，用於本主題和範例應用程式中的示範用途。</span><span class="sxs-lookup"><span data-stu-id="85846-484">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="85846-485">ASP.NET Core 應用程式的整合測試不支援或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="85846-485">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="85846-486">您可以使用其他剖析器，例如 [ (HAP) 的 Html 敏捷套件 ](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="85846-486">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="85846-487">另一種方法是撰寫程式碼來處理 antiforgery 系統的要求驗證權杖，並 cookie 直接 antiforgery。</span><span class="sxs-lookup"><span data-stu-id="85846-487">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="85846-488">使用 WithWebHostBuilder 自訂用戶端</span><span class="sxs-lookup"><span data-stu-id="85846-488">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="85846-489">在測試方法中需要進行其他設定時， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 會建立一個新的， `WebApplicationFactory` 其中包含由設定進一步自訂的 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="85846-489">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="85846-490">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的測試方法會示範的使用方式 `WithWebHostBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="85846-490">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="85846-491">這項測試會在 SUT 中觸發表單提交，在資料庫中執行記錄刪除。</span><span class="sxs-lookup"><span data-stu-id="85846-491">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="85846-492">因為類別中的另一個測試 `IndexPageTests` 會執行刪除資料庫中所有記錄的作業，而且可能會在方法之前執行，所以會 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 在此測試方法中重新植入資料庫，以確保有記錄存在，以便讓 SUT 刪除。</span><span class="sxs-lookup"><span data-stu-id="85846-492">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="85846-493">在 sut 的 `messages` 要求中，會模擬在 sut 中選取表單的第一個 [刪除] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="85846-493">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="85846-494">用戶端選項</span><span class="sxs-lookup"><span data-stu-id="85846-494">Client options</span></span>

<span data-ttu-id="85846-495">下表顯示建立實例時可使用的預設 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="85846-495">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="85846-496">選項</span><span class="sxs-lookup"><span data-stu-id="85846-496">Option</span></span> | <span data-ttu-id="85846-497">描述</span><span class="sxs-lookup"><span data-stu-id="85846-497">Description</span></span> | <span data-ttu-id="85846-498">預設</span><span class="sxs-lookup"><span data-stu-id="85846-498">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="85846-499">Request.allowautoredirect</span><span class="sxs-lookup"><span data-stu-id="85846-499">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="85846-500">取得或設定 `HttpClient` 實例是否應該自動遵循重新導向回應。</span><span class="sxs-lookup"><span data-stu-id="85846-500">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="85846-501">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="85846-501">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="85846-502">取得或設定實例的基底位址 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="85846-502">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| <span data-ttu-id="85846-503">[句 Cookie 柄](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span><span class="sxs-lookup"><span data-stu-id="85846-503">[HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)</span></span> | <span data-ttu-id="85846-504">取得或設定 `HttpClient` 實例是否應該處理 cookie s。</span><span class="sxs-lookup"><span data-stu-id="85846-504">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="85846-505">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="85846-505">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="85846-506">取得或設定實例應遵循的重新導向回應數目上限 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="85846-506">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="85846-507">7</span><span class="sxs-lookup"><span data-stu-id="85846-507">7</span></span> |

<span data-ttu-id="85846-508">建立 `WebApplicationFactoryClientOptions` 類別，並將它傳遞給 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (預設值會顯示在程式碼範例) 中：</span><span class="sxs-lookup"><span data-stu-id="85846-508">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="85846-509">插入 mock 服務</span><span class="sxs-lookup"><span data-stu-id="85846-509">Inject mock services</span></span>

<span data-ttu-id="85846-510">您可以在主機建立器上呼叫 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，以在測試中覆寫服務。</span><span class="sxs-lookup"><span data-stu-id="85846-510">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="85846-511">**若要插入模擬服務，則必須具有 `Startup` 具有方法的類別 `Startup.ConfigureServices` 。**</span><span class="sxs-lookup"><span data-stu-id="85846-511">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="85846-512">範例 SUT 包含會傳回報價的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="85846-512">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="85846-513">當要求索引頁面時，引號會內嵌在索引頁面的隱藏欄位中。</span><span class="sxs-lookup"><span data-stu-id="85846-513">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="85846-514">*Services/IQuoteService .cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-514">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="85846-515">*Services/QuoteService .cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-515">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="85846-516">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-516">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="85846-517">*Pages/Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-517">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="85846-518">*Pages/Index .cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-518">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="85846-519">執行 SUT 應用程式時，會產生下列標記：</span><span class="sxs-lookup"><span data-stu-id="85846-519">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="85846-520">若要在整合測試中測試服務和報價插入，模擬服務會由測試插入至 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-520">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="85846-521">模擬服務會將應用程式取代為 `QuoteService` 測試應用程式所提供的服務，稱為 `TestQuoteService` ：</span><span class="sxs-lookup"><span data-stu-id="85846-521">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="85846-522">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="85846-522">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="85846-523">`ConfigureTestServices` 會呼叫，且已註冊範圍服務：</span><span class="sxs-lookup"><span data-stu-id="85846-523">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="85846-524">測試執行期間所產生的標記會反映所提供的報價文字 `TestQuoteService` ，因此判斷提示會通過：</span><span class="sxs-lookup"><span data-stu-id="85846-524">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="85846-525">Mock 驗證</span><span class="sxs-lookup"><span data-stu-id="85846-525">Mock authentication</span></span>

<span data-ttu-id="85846-526">類別中的測試 `AuthTests` 會檢查安全的端點：</span><span class="sxs-lookup"><span data-stu-id="85846-526">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="85846-527">將未經驗證的使用者重新導向至應用程式的登入頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-527">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="85846-528">傳回已驗證使用者的內容。</span><span class="sxs-lookup"><span data-stu-id="85846-528">Returns content for an authenticated user.</span></span>

<span data-ttu-id="85846-529">在 SUT 中， `/SecurePage` 頁面會使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 慣例，將 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 套用至頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-529">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="85846-530">如需詳細資訊，請參閱[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="85846-530">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="85846-531">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 測試中， [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 會設定為 [不允許重新導向]，方法是將 [request.allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 設定為 `false` ：</span><span class="sxs-lookup"><span data-stu-id="85846-531">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="85846-532">藉由不允許用戶端遵循重新導向，可進行下列檢查：</span><span class="sxs-lookup"><span data-stu-id="85846-532">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="85846-533">在重新導向至登入頁面之後，您可以根據預期的 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode) 來檢查您所傳回的狀態碼，而不是最後的狀態碼，這會是 [HttpStatusCode。確定](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="85846-533">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="85846-534">`Location`檢查回應標頭中的標頭值，以確認它的開頭 `http://localhost/Identity/Account/Login` 不是最後一個登入頁面回應，而且 `Location` 標頭不會出現。</span><span class="sxs-lookup"><span data-stu-id="85846-534">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="85846-535">測試應用程式可以在 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模擬，以便測試驗證和授權的層面。</span><span class="sxs-lookup"><span data-stu-id="85846-535">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="85846-536">基本案例會傳回 [AuthenticateResult。 Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="85846-536">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="85846-537">`TestAuthHandler`當驗證配置設定為已註冊的位置時，會呼叫來驗證使用者 `Test` `AddAuthentication` `ConfigureTestServices` ：</span><span class="sxs-lookup"><span data-stu-id="85846-537">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="85846-538">如需的詳細資訊 `WebApplicationFactoryClientOptions` ，請參閱 [用戶端選項](#client-options) 一節。</span><span class="sxs-lookup"><span data-stu-id="85846-538">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="85846-539">設定環境</span><span class="sxs-lookup"><span data-stu-id="85846-539">Set the environment</span></span>

<span data-ttu-id="85846-540">根據預設，已將 SUT 的主機和應用程式環境設定為使用開發環境。</span><span class="sxs-lookup"><span data-stu-id="85846-540">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="85846-541">若要覆寫 SUT 的環境：</span><span class="sxs-lookup"><span data-stu-id="85846-541">To override the SUT's environment:</span></span>

* <span data-ttu-id="85846-542">設定 `ASPNETCORE_ENVIRONMENT` 環境變數 (例如，、 `Staging` `Production` 或其他自訂值，例如 `Testing`) 。</span><span class="sxs-lookup"><span data-stu-id="85846-542">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="85846-543">`CreateWebHostBuilder`在測試應用程式中覆寫以讀取 `ASPNETCORE_ENVIRONMENT` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="85846-543">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="85846-544">您也可以直接在自訂中的主機建立器上設定環境 <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> ：</span><span class="sxs-lookup"><span data-stu-id="85846-544">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="85846-545">測試基礎結構如何推斷應用程式內容根路徑</span><span class="sxs-lookup"><span data-stu-id="85846-545">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="85846-546">此函式會藉 `WebApplicationFactory` 由在包含整合測試與元件相等的元件上搜尋[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) ，來推斷應用程式[內容的根](xref:fundamentals/index#content-root)路徑 `TEntryPoint` `System.Reflection.Assembly.FullName` 。</span><span class="sxs-lookup"><span data-stu-id="85846-546">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="85846-547">如果找不到具有正確索引鍵的屬性，則會 `WebApplicationFactory` 切換回以搜尋方案檔 (*.Sln*) 並將 `TEntryPoint` 元件名稱附加至方案目錄。</span><span class="sxs-lookup"><span data-stu-id="85846-547">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="85846-548">應用程式根目錄 (內容根路徑) 用來探索視圖和內容檔案。</span><span class="sxs-lookup"><span data-stu-id="85846-548">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="85846-549">停用陰影複製</span><span class="sxs-lookup"><span data-stu-id="85846-549">Disable shadow copying</span></span>

<span data-ttu-id="85846-550">陰影複製會導致測試在和輸出目錄不同的目錄中執行。</span><span class="sxs-lookup"><span data-stu-id="85846-550">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="85846-551">若要讓測試正常運作，必須停用陰影複製。</span><span class="sxs-lookup"><span data-stu-id="85846-551">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="85846-552">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)會使用 xUnit 並停用 xUnit 的陰影複製，方法是在具有正確設定的檔案*上包含xunit.runner.js* 。</span><span class="sxs-lookup"><span data-stu-id="85846-552">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="85846-553">如需詳細資訊，請參閱 [使用 JSON 設定 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="85846-553">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="85846-554">使用下列內容，將檔案 \* 上的xunit.runner.js\* 新增至測試專案的根目錄：</span><span class="sxs-lookup"><span data-stu-id="85846-554">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="85846-555">如果使用 Visual Studio，請將檔案的 [ **複製到輸出目錄** ] 屬性設定為 [ **永遠複製**]。</span><span class="sxs-lookup"><span data-stu-id="85846-555">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="85846-556">如果未使用 Visual Studio，請將 `Content` 目標新增至測試應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="85846-556">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="85846-557">物件的處置</span><span class="sxs-lookup"><span data-stu-id="85846-557">Disposal of objects</span></span>

<span data-ttu-id="85846-558">`IClassFixture`執行實施測試之後，當 xUnit 處置[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)時，會處置[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="85846-558">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="85846-559">如果開發人員所具現化的物件需要處置，請在執行時處置它們 `IClassFixture` 。</span><span class="sxs-lookup"><span data-stu-id="85846-559">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="85846-560">如需詳細資訊，請參閱 [執行 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="85846-560">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="85846-561">整合測試範例</span><span class="sxs-lookup"><span data-stu-id="85846-561">Integration tests sample</span></span>

<span data-ttu-id="85846-562">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)是由兩個應用程式所組成：</span><span class="sxs-lookup"><span data-stu-id="85846-562">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="85846-563">應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-563">App</span></span> | <span data-ttu-id="85846-564">專案目錄</span><span class="sxs-lookup"><span data-stu-id="85846-564">Project directory</span></span> | <span data-ttu-id="85846-565">描述</span><span class="sxs-lookup"><span data-stu-id="85846-565">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="85846-566"> (在 SUT) 的訊息應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-566">Message app (the SUT)</span></span> | <span data-ttu-id="85846-567">*src/ Razor PagesProject*</span><span class="sxs-lookup"><span data-stu-id="85846-567">*src/RazorPagesProject*</span></span> | <span data-ttu-id="85846-568">允許使用者新增、刪除、刪除所有訊息，以及分析訊息。</span><span class="sxs-lookup"><span data-stu-id="85846-568">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="85846-569">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-569">Test app</span></span> | <span data-ttu-id="85846-570">*測試/ Razor PagesProject*</span><span class="sxs-lookup"><span data-stu-id="85846-570">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="85846-571">用於整合測試的 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-571">Used to integration test the SUT.</span></span> |

<span data-ttu-id="85846-572">您可以使用 IDE 的內建測試功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）來執行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-572">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="85846-573">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesProject* ] 目錄中的命令提示字元執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="85846-573">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="85846-574"> (的 SUT) 組織的訊息應用程式</span><span class="sxs-lookup"><span data-stu-id="85846-574">Message app (SUT) organization</span></span>

<span data-ttu-id="85846-575">SUT 是 Razor 具有下列特性的頁面訊息系統：</span><span class="sxs-lookup"><span data-stu-id="85846-575">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="85846-576">應用程式的 [索引] 頁面 (*pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法，可控制訊息的新增、刪除和分析 (每個訊息) 的平均單字。</span><span class="sxs-lookup"><span data-stu-id="85846-576">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="85846-577">訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。</span><span class="sxs-lookup"><span data-stu-id="85846-577">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="85846-578">`Text`屬性是必要的，且限制為200個字元。</span><span class="sxs-lookup"><span data-stu-id="85846-578">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="85846-579">訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/)&#8224; 來儲存。</span><span class="sxs-lookup"><span data-stu-id="85846-579">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="85846-580">應用程式在其資料庫內容類別中包含 (DAL) 的資料存取層， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。</span><span class="sxs-lookup"><span data-stu-id="85846-580">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="85846-581">如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。</span><span class="sxs-lookup"><span data-stu-id="85846-581">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="85846-582">應用程式包含 `/SecurePage` 只能由已驗證的使用者存取的。</span><span class="sxs-lookup"><span data-stu-id="85846-582">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="85846-583">&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。</span><span class="sxs-lookup"><span data-stu-id="85846-583">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="85846-584">本主題使用 [xUnit](https://xunit.github.io/) 測試架構。</span><span class="sxs-lookup"><span data-stu-id="85846-584">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="85846-585">跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="85846-585">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="85846-586">雖然應用程式不會使用存放庫模式，而不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。</span><span class="sxs-lookup"><span data-stu-id="85846-586">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="85846-587">如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 [測試控制器邏輯](/aspnet/core/mvc/controllers/testing) (範例會) 執行儲存機制模式。</span><span class="sxs-lookup"><span data-stu-id="85846-587">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="85846-588">測試應用程式組織</span><span class="sxs-lookup"><span data-stu-id="85846-588">Test app organization</span></span>

<span data-ttu-id="85846-589">測試應用程式是 [ *測試/ Razor PagesProject* ] 目錄中的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="85846-589">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="85846-590">測試應用程式目錄</span><span class="sxs-lookup"><span data-stu-id="85846-590">Test app directory</span></span> | <span data-ttu-id="85846-591">描述</span><span class="sxs-lookup"><span data-stu-id="85846-591">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="85846-592">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="85846-592">*AuthTests*</span></span> | <span data-ttu-id="85846-593">包含的測試方法：</span><span class="sxs-lookup"><span data-stu-id="85846-593">Contains test methods for:</span></span><ul><li><span data-ttu-id="85846-594">未經驗證的使用者存取安全的頁面。</span><span class="sxs-lookup"><span data-stu-id="85846-594">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="85846-595">使用模擬以已驗證的使用者存取安全的頁面 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> 。</span><span class="sxs-lookup"><span data-stu-id="85846-595">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="85846-596">取得 GitHub 使用者設定檔，並檢查設定檔的使用者登入。</span><span class="sxs-lookup"><span data-stu-id="85846-596">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="85846-597">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="85846-597">*BasicTests*</span></span> | <span data-ttu-id="85846-598">包含路由和內容類型的測試方法。</span><span class="sxs-lookup"><span data-stu-id="85846-598">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="85846-599">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="85846-599">*IntegrationTests*</span></span> | <span data-ttu-id="85846-600">包含使用自訂類別之 [索引] 頁面的整合測試 `WebApplicationFactory` 。</span><span class="sxs-lookup"><span data-stu-id="85846-600">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="85846-601">*協助程式/公用程式*</span><span class="sxs-lookup"><span data-stu-id="85846-601">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="85846-602">*Utilities.cs* 包含 `InitializeDbForTests` 用來將測試資料植入資料庫的方法。</span><span class="sxs-lookup"><span data-stu-id="85846-602">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="85846-603">*HtmlHelpers.cs* 會提供方法，以傳回 `IHtmlDocument` 測試方法所使用的 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="85846-603">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="85846-604">*HttpClientExtensions.cs* 會提供的多載， `SendAsync` 以將要求提交至 SUT。</span><span class="sxs-lookup"><span data-stu-id="85846-604">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="85846-605">測試架構為 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="85846-605">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="85846-606">整合測試是使用 [AspNetCore TestHost](/dotnet/api/microsoft.aspnetcore.testhost)，其中包含 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="85846-606">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="85846-607">由於使用 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝來設定測試主機和測試伺服器，因此 `TestHost` 和套件不需要測試應用程式的專案檔 `TestServer` 或開發人員在測試應用程式中進行直接封裝參考。</span><span class="sxs-lookup"><span data-stu-id="85846-607">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="85846-608">**植入資料庫以進行測試**</span><span class="sxs-lookup"><span data-stu-id="85846-608">**Seeding the database for testing**</span></span>

<span data-ttu-id="85846-609">在測試執行之前，整合測試通常需要資料庫中的小型資料集。</span><span class="sxs-lookup"><span data-stu-id="85846-609">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="85846-610">例如，刪除測試會呼叫資料庫記錄刪除，因此資料庫必須至少有一筆記錄，刪除要求才能成功。</span><span class="sxs-lookup"><span data-stu-id="85846-610">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="85846-611">範例應用程式會使用 *Utilities.cs* 中的三個訊息來植入資料庫，測試可在執行時使用這些訊息：</span><span class="sxs-lookup"><span data-stu-id="85846-611">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="85846-612">其他資源</span><span class="sxs-lookup"><span data-stu-id="85846-612">Additional resources</span></span>

* [<span data-ttu-id="85846-613">單元測試</span><span class="sxs-lookup"><span data-stu-id="85846-613">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
