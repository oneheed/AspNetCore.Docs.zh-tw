---
title: Razor ASP.NET Core 中的頁面單元測試
author: rick-anderson
description: 瞭解如何為 Razor 頁面應用程式建立單元測試。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: bb376412daafd49b307c3c13ea7c88f34628f1c3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634835"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="a994b-103">Razor ASP.NET Core 中的頁面單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-103">Razor Pages unit tests in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a994b-104">ASP.NET Core 支援 Razor 頁面應用程式的單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-104">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="a994b-105">資料存取層 (DAL) 和頁面模型的測試有助於確保：</span><span class="sxs-lookup"><span data-stu-id="a994b-105">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="a994b-106">RazorPages 應用程式的各個部分在應用程式結構期間，會獨立地以一個單位來運作。</span><span class="sxs-lookup"><span data-stu-id="a994b-106">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="a994b-107">類別和方法的責任範圍有限。</span><span class="sxs-lookup"><span data-stu-id="a994b-107">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="a994b-108">應用程式的行為方式有其他檔。</span><span class="sxs-lookup"><span data-stu-id="a994b-108">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="a994b-109">在自動化的建立和部署期間，會發現回歸（這是程式碼更新所帶來的錯誤）。</span><span class="sxs-lookup"><span data-stu-id="a994b-109">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="a994b-110">本主題假設您對 Razor 頁面應用程式和單元測試有基本的瞭解。</span><span class="sxs-lookup"><span data-stu-id="a994b-110">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="a994b-111">如果您不熟悉 Razor 頁面應用程式或測試概念，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="a994b-111">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="a994b-112">使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-112">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="a994b-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a994b-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a994b-114">範例專案是由兩個應用程式所組成：</span><span class="sxs-lookup"><span data-stu-id="a994b-114">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="a994b-115">應用程式</span><span class="sxs-lookup"><span data-stu-id="a994b-115">App</span></span>         | <span data-ttu-id="a994b-116">專案資料夾</span><span class="sxs-lookup"><span data-stu-id="a994b-116">Project folder</span></span>                     | <span data-ttu-id="a994b-117">描述</span><span class="sxs-lookup"><span data-stu-id="a994b-117">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="a994b-118">訊息應用程式</span><span class="sxs-lookup"><span data-stu-id="a994b-118">Message app</span></span> | <span data-ttu-id="a994b-119">*src/ Razor PagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="a994b-119">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="a994b-120">可讓使用者新增訊息、刪除一則訊息、刪除所有訊息，以及分析訊息 (尋找每個訊息的平均單字數目) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-120">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="a994b-121">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="a994b-121">Test app</span></span>    | <span data-ttu-id="a994b-122">*測試/ Razor PagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="a994b-122">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="a994b-123">用來對訊息應用程式的 DAL 和索引頁面模型進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-123">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="a994b-124">您可以使用 IDE 的內建測試功能來執行測試，例如 [Visual Studio](/visualstudio/test/unit-test-your-code) 或 [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)。</span><span class="sxs-lookup"><span data-stu-id="a994b-124">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="a994b-125">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesTestSample* ] 資料夾的命令提示字元中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a994b-125">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="a994b-126">訊息應用程式組織</span><span class="sxs-lookup"><span data-stu-id="a994b-126">Message app organization</span></span>

<span data-ttu-id="a994b-127">訊息應用程式是 Razor 具有下列特性的頁面訊息系統：</span><span class="sxs-lookup"><span data-stu-id="a994b-127">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="a994b-128">應用程式的 [索引] 頁面 (*Pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法來控制訊息的新增、刪除和分析， (尋找每個訊息) 的平均單字數。</span><span class="sxs-lookup"><span data-stu-id="a994b-128">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="a994b-129">訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-129">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="a994b-130">`Text`屬性是必要的，且限制為200個字元。</span><span class="sxs-lookup"><span data-stu-id="a994b-130">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="a994b-131">訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/)&#8224; 來儲存。</span><span class="sxs-lookup"><span data-stu-id="a994b-131">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="a994b-132">應用程式在其資料庫內容類別中包含 DAL， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-132">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="a994b-133">DAL 方法會標示為 `virtual` ，可讓您模擬要在測試中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="a994b-133">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="a994b-134">如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。</span><span class="sxs-lookup"><span data-stu-id="a994b-134">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="a994b-135">這些 *植* 入的訊息也會在測試中使用。</span><span class="sxs-lookup"><span data-stu-id="a994b-135">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="a994b-136">&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-136">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="a994b-137">本主題使用 [xUnit](https://xunit.github.io/) 測試架構。</span><span class="sxs-lookup"><span data-stu-id="a994b-137">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="a994b-138">跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="a994b-138">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="a994b-139">雖然範例應用程式不會使用存放庫模式，且不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。</span><span class="sxs-lookup"><span data-stu-id="a994b-139">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="a994b-140">如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 (範例中的實 <xref:mvc/controllers/testing> 作為儲存機制模式) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-140">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="a994b-141">測試應用程式組織</span><span class="sxs-lookup"><span data-stu-id="a994b-141">Test app organization</span></span>

<span data-ttu-id="a994b-142">測試應用程式是 [ *測試/ Razor PagesTestSample* ] 資料夾內的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="a994b-142">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="a994b-143">測試應用程式資料夾</span><span class="sxs-lookup"><span data-stu-id="a994b-143">Test app folder</span></span> | <span data-ttu-id="a994b-144">描述</span><span class="sxs-lookup"><span data-stu-id="a994b-144">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="a994b-145">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="a994b-145">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="a994b-146">*DataAccessLayerTest.cs* 包含 DAL 的單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-146">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="a994b-147">*IndexPageTests.cs* 包含索引頁面模型的單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-147">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="a994b-148">*公用程式*</span><span class="sxs-lookup"><span data-stu-id="a994b-148">*Utilities*</span></span>     | <span data-ttu-id="a994b-149">包含 `TestDbContextOptions` 用來為每個 DAL 單元測試建立新資料庫內容選項的方法，以便將資料庫重設為每個測試的基準條件。</span><span class="sxs-lookup"><span data-stu-id="a994b-149">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="a994b-150">測試架構為 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="a994b-150">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="a994b-151">物件模擬架構為 [Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="a994b-151">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="a994b-152">資料存取層 (DAL 的單元測試) </span><span class="sxs-lookup"><span data-stu-id="a994b-152">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="a994b-153">訊息應用程式具有具有四個包含在類別中的方法 `AppDbContext` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-153">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="a994b-154">每個方法在測試應用程式中都有一個或兩個單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-154">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="a994b-155">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="a994b-155">DAL method</span></span>               | <span data-ttu-id="a994b-156">函式</span><span class="sxs-lookup"><span data-stu-id="a994b-156">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="a994b-157">從資料庫取得，並 `List<Message>` 依屬性排序 `Text` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-157">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="a994b-158">將加入 `Message` 至資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-158">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="a994b-159">`Message`從資料庫中刪除所有專案。</span><span class="sxs-lookup"><span data-stu-id="a994b-159">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="a994b-160">`Message`從資料庫中刪除單一 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-160">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="a994b-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptions>為每個測試建立新的時，DAL 需要單元測試 `AppDbContext` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-161">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="a994b-162">針對每個測試建立的其中一種方法 `DbContextOptions` 是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="a994b-162">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="a994b-163">這種方法的問題在於，每個測試都會以先前測試所用的任何狀態來接收資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-163">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="a994b-164">當您嘗試撰寫不會干擾彼此的不可部分完成單元測試時，這可能會造成問題。</span><span class="sxs-lookup"><span data-stu-id="a994b-164">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="a994b-165">若要強制將 `AppDbContext` 新的資料庫內容用於每個測試，請提供以 `DbContextOptions` 新的服務提供者為基礎的實例。</span><span class="sxs-lookup"><span data-stu-id="a994b-165">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="a994b-166">測試應用程式會示範如何使用其 `Utilities` class 方法 (test `TestDbContextOptions` */ Razor PagesTestSample. 測試/公用事業/utilities*) ：</span><span class="sxs-lookup"><span data-stu-id="a994b-166">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="a994b-167">`DbContextOptions`在 DAL 單元測試中使用，可讓每個測試以不可部分完成的方式以全新的資料庫實例執行：</span><span class="sxs-lookup"><span data-stu-id="a994b-167">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="a994b-168">類別中的每個測試方法 `DataAccessLayerTest` (*>--run-unittests/DataAccessLayerTest*) 遵循類似的排列-Act 判斷提示模式：</span><span class="sxs-lookup"><span data-stu-id="a994b-168">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="a994b-169">排列：資料庫設定為測試及/或定義預期的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-169">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="a994b-170">Act：執行測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-170">Act: The test is executed.</span></span>
1. <span data-ttu-id="a994b-171">判斷提示：判斷測試結果是否成功的判斷提示。</span><span class="sxs-lookup"><span data-stu-id="a994b-171">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="a994b-172">例如， `DeleteMessageAsync` 方法會負責移除其 `Id` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 所識別的單一訊息：</span><span class="sxs-lookup"><span data-stu-id="a994b-172">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="a994b-173">這種方法有兩種測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-173">There are two tests for this method.</span></span> <span data-ttu-id="a994b-174">當訊息存在於資料庫中時，一項測試會檢查此方法是否會刪除訊息。</span><span class="sxs-lookup"><span data-stu-id="a994b-174">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="a994b-175">另一種方法會測試如果刪除的訊息不存在，資料庫不會變更 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-175">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="a994b-176">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="a994b-176">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="a994b-177">首先，此方法會執行「排列」步驟，以進行「動作」步驟的準備。</span><span class="sxs-lookup"><span data-stu-id="a994b-177">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="a994b-178">系統會取得並保存植入訊息 `seedMessages` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-178">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="a994b-179">植入訊息會儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="a994b-179">The seeding messages are saved into the database.</span></span> <span data-ttu-id="a994b-180">具有 `Id` 之的訊息 `1` 會設定為 [刪除]。</span><span class="sxs-lookup"><span data-stu-id="a994b-180">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="a994b-181">當 `DeleteMessageAsync` 執行方法時，預期的訊息應該會有所有的訊息，但不包括包含的訊息 `Id` `1` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-181">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="a994b-182">`expectedMessages`變數表示此預期的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-182">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="a994b-183">方法 `DeleteMessageAsync` 會執行：方法會傳遞的 `recId` `1` ：</span><span class="sxs-lookup"><span data-stu-id="a994b-183">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="a994b-184">最後，方法 `Messages` 會從內容中取得，並將它與判斷提示兩者相等的判斷提示比較 `expectedMessages` ：</span><span class="sxs-lookup"><span data-stu-id="a994b-184">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="a994b-185">為了比較兩者 `List<Message>` 相同：</span><span class="sxs-lookup"><span data-stu-id="a994b-185">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="a994b-186">訊息的排序依據 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-186">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="a994b-187">訊息配對會在屬性上進行比較 `Text` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-187">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="a994b-188">類似的測試方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 會檢查嘗試刪除不存在之訊息的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-188">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="a994b-189">在此情況下，資料庫中的預期訊息應該會在執行方法之後等於實際的訊息 `DeleteMessageAsync` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-189">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="a994b-190">資料庫的內容應該不會有任何變更：</span><span class="sxs-lookup"><span data-stu-id="a994b-190">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="a994b-191">頁面模型方法的單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-191">Unit tests of the page model methods</span></span>

<span data-ttu-id="a994b-192">另一組單元測試負責測試頁面模型方法。</span><span class="sxs-lookup"><span data-stu-id="a994b-192">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="a994b-193">在訊息應用程式中，索引頁面模型可在 `IndexModel` *src/ Razor PagesTestSample/Pages/Index. .cs*的類別中找到。</span><span class="sxs-lookup"><span data-stu-id="a994b-193">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="a994b-194">頁面模型方法</span><span class="sxs-lookup"><span data-stu-id="a994b-194">Page model method</span></span> | <span data-ttu-id="a994b-195">函式</span><span class="sxs-lookup"><span data-stu-id="a994b-195">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="a994b-196">使用方法，從 DAL 取得適用于 UI 的訊息 `GetMessagesAsync` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-196">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="a994b-197">如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，則呼叫會 `AddMessageAsync` 將訊息加入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-197">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="a994b-198">呼叫 `DeleteAllMessagesAsync` 以刪除資料庫中的所有訊息。</span><span class="sxs-lookup"><span data-stu-id="a994b-198">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="a994b-199">執行 `DeleteMessageAsync` 以刪除具有指定之的訊息 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-199">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="a994b-200">如果資料庫中有一或多個訊息，則會計算每個訊息的平均單字數。</span><span class="sxs-lookup"><span data-stu-id="a994b-200">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="a994b-201">頁面模型方法會使用類別中的七個測試進行測試 `IndexPageTests` (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-201">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="a994b-202">測試會使用熟悉的「排列-判斷提示-Act」模式。</span><span class="sxs-lookup"><span data-stu-id="a994b-202">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="a994b-203">這些測試著重于：</span><span class="sxs-lookup"><span data-stu-id="a994b-203">These tests focus on:</span></span>

* <span data-ttu-id="a994b-204">判斷在 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 無效時，方法是否遵循正確的行為。</span><span class="sxs-lookup"><span data-stu-id="a994b-204">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="a994b-205">確認方法會產生正確的 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 。</span><span class="sxs-lookup"><span data-stu-id="a994b-205">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="a994b-206">檢查是否已正確設定屬性值。</span><span class="sxs-lookup"><span data-stu-id="a994b-206">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="a994b-207">這組測試通常會模擬 DAL 的方法，為執行頁面模型方法的 Act 步驟產生預期的資料。</span><span class="sxs-lookup"><span data-stu-id="a994b-207">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="a994b-208">例如，的 `GetMessagesAsync` 方法 `AppDbContext` 會模擬以產生輸出。</span><span class="sxs-lookup"><span data-stu-id="a994b-208">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="a994b-209">當頁面模型方法執行此方法時，mock 會傳回結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-209">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="a994b-210">資料不是來自資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-210">The data doesn't come from the database.</span></span> <span data-ttu-id="a994b-211">這會建立可預測、可靠的測試條件，以便在頁面模型測試中使用 DAL。</span><span class="sxs-lookup"><span data-stu-id="a994b-211">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="a994b-212">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`測試會顯示如何 `GetMessagesAsync` 針對頁面模型模擬方法：</span><span class="sxs-lookup"><span data-stu-id="a994b-212">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="a994b-213">當 `OnGetAsync` 方法在 Act 步驟中執行時，它會呼叫頁面模型的 `GetMessagesAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="a994b-213">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="a994b-214">單元測試動作步驟 (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="a994b-214">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="a994b-215">`IndexPage` 頁面模型的 `OnGetAsync` 方法 (*src/ Razor PagesTestSample/Pages/Index. cshtml*) ：</span><span class="sxs-lookup"><span data-stu-id="a994b-215">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="a994b-216">`GetMessagesAsync`DAL 中的方法不會傳回此方法呼叫的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-216">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="a994b-217">方法的模擬版本會傳回結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-217">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="a994b-218">在 `Assert` 步驟中， () 的實際訊息 `actualMessages` 是從 `Messages` 頁面模型的屬性指派。</span><span class="sxs-lookup"><span data-stu-id="a994b-218">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="a994b-219">當指派訊息時，也會執行類型檢查。</span><span class="sxs-lookup"><span data-stu-id="a994b-219">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="a994b-220">預期和實際的訊息會以其屬性進行比較 `Text` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-220">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="a994b-221">測試會判斷兩個 `List<Message>` 實例是否包含相同的訊息。</span><span class="sxs-lookup"><span data-stu-id="a994b-221">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="a994b-222">此群組中的其他測試會建立頁面模型物件，這些物件包含 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> 、、 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> ， <xref:Microsoft.AspNetCore.Mvc.ActionContext> 以建立 `PageContext` 、 `ViewDataDictionary` 和 `PageContext` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-222">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="a994b-223">這些都適用于進行測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-223">These are useful in conducting tests.</span></span> <span data-ttu-id="a994b-224">例如，訊息應用程式 `ModelState` 會建立錯誤， <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 以檢查 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 執行時是否傳回有效的 `OnPostAddMessageAsync` ：</span><span class="sxs-lookup"><span data-stu-id="a994b-224">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="a994b-225">其他資源</span><span class="sxs-lookup"><span data-stu-id="a994b-225">Additional resources</span></span>

* [<span data-ttu-id="a994b-226">使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-226">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="a994b-227">[對程式碼進行單元測試](/visualstudio/test/unit-test-your-code) (Visual Studio) </span><span class="sxs-lookup"><span data-stu-id="a994b-227">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="a994b-228">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="a994b-228">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="a994b-229">使用 Visual Studio for Mac 在 macOS 上建置完整的 .NET Core 方案</span><span class="sxs-lookup"><span data-stu-id="a994b-229">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="a994b-230">開始使用 xUnit.net：搭配使用 .NET Core 與 .NET SDK 命令列</span><span class="sxs-lookup"><span data-stu-id="a994b-230">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="a994b-231">Moq</span><span class="sxs-lookup"><span data-stu-id="a994b-231">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="a994b-232">Moq 快速入門</span><span class="sxs-lookup"><span data-stu-id="a994b-232">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a994b-233">ASP.NET Core 支援 Razor 頁面應用程式的單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-233">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="a994b-234">資料存取層 (DAL) 和頁面模型的測試有助於確保：</span><span class="sxs-lookup"><span data-stu-id="a994b-234">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="a994b-235">RazorPages 應用程式的各個部分在應用程式結構期間，會獨立地以一個單位來運作。</span><span class="sxs-lookup"><span data-stu-id="a994b-235">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="a994b-236">類別和方法的責任範圍有限。</span><span class="sxs-lookup"><span data-stu-id="a994b-236">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="a994b-237">應用程式的行為方式有其他檔。</span><span class="sxs-lookup"><span data-stu-id="a994b-237">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="a994b-238">在自動化的建立和部署期間，會發現回歸（這是程式碼更新所帶來的錯誤）。</span><span class="sxs-lookup"><span data-stu-id="a994b-238">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="a994b-239">本主題假設您對 Razor 頁面應用程式和單元測試有基本的瞭解。</span><span class="sxs-lookup"><span data-stu-id="a994b-239">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="a994b-240">如果您不熟悉 Razor 頁面應用程式或測試概念，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="a994b-240">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="a994b-241">使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-241">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="a994b-242">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a994b-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a994b-243">範例專案是由兩個應用程式所組成：</span><span class="sxs-lookup"><span data-stu-id="a994b-243">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="a994b-244">應用程式</span><span class="sxs-lookup"><span data-stu-id="a994b-244">App</span></span>         | <span data-ttu-id="a994b-245">專案資料夾</span><span class="sxs-lookup"><span data-stu-id="a994b-245">Project folder</span></span>                     | <span data-ttu-id="a994b-246">描述</span><span class="sxs-lookup"><span data-stu-id="a994b-246">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="a994b-247">訊息應用程式</span><span class="sxs-lookup"><span data-stu-id="a994b-247">Message app</span></span> | <span data-ttu-id="a994b-248">*src/ Razor PagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="a994b-248">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="a994b-249">可讓使用者新增訊息、刪除一則訊息、刪除所有訊息，以及分析訊息 (尋找每個訊息的平均單字數目) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-249">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="a994b-250">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="a994b-250">Test app</span></span>    | <span data-ttu-id="a994b-251">*測試/ Razor PagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="a994b-251">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="a994b-252">用來對訊息應用程式的 DAL 和索引頁面模型進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-252">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="a994b-253">您可以使用 IDE 的內建測試功能來執行測試，例如 [Visual Studio](/visualstudio/test/unit-test-your-code) 或 [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)。</span><span class="sxs-lookup"><span data-stu-id="a994b-253">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="a994b-254">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesTestSample* ] 資料夾的命令提示字元中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a994b-254">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="a994b-255">訊息應用程式組織</span><span class="sxs-lookup"><span data-stu-id="a994b-255">Message app organization</span></span>

<span data-ttu-id="a994b-256">訊息應用程式是 Razor 具有下列特性的頁面訊息系統：</span><span class="sxs-lookup"><span data-stu-id="a994b-256">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="a994b-257">應用程式的 [索引] 頁面 (*Pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法來控制訊息的新增、刪除和分析， (尋找每個訊息) 的平均單字數。</span><span class="sxs-lookup"><span data-stu-id="a994b-257">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="a994b-258">訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-258">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="a994b-259">`Text`屬性是必要的，且限制為200個字元。</span><span class="sxs-lookup"><span data-stu-id="a994b-259">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="a994b-260">訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/)&#8224; 來儲存。</span><span class="sxs-lookup"><span data-stu-id="a994b-260">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="a994b-261">應用程式在其資料庫內容類別中包含 DAL， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-261">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="a994b-262">DAL 方法會標示為 `virtual` ，可讓您模擬要在測試中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="a994b-262">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="a994b-263">如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。</span><span class="sxs-lookup"><span data-stu-id="a994b-263">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="a994b-264">這些 *植* 入的訊息也會在測試中使用。</span><span class="sxs-lookup"><span data-stu-id="a994b-264">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="a994b-265">&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-265">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="a994b-266">本主題使用 [xUnit](https://xunit.github.io/) 測試架構。</span><span class="sxs-lookup"><span data-stu-id="a994b-266">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="a994b-267">跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="a994b-267">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="a994b-268">雖然範例應用程式不會使用存放庫模式，且不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。</span><span class="sxs-lookup"><span data-stu-id="a994b-268">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="a994b-269">如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 (範例中的實 <xref:mvc/controllers/testing> 作為儲存機制模式) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-269">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="a994b-270">測試應用程式組織</span><span class="sxs-lookup"><span data-stu-id="a994b-270">Test app organization</span></span>

<span data-ttu-id="a994b-271">測試應用程式是 [ *測試/ Razor PagesTestSample* ] 資料夾內的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="a994b-271">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="a994b-272">測試應用程式資料夾</span><span class="sxs-lookup"><span data-stu-id="a994b-272">Test app folder</span></span> | <span data-ttu-id="a994b-273">描述</span><span class="sxs-lookup"><span data-stu-id="a994b-273">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="a994b-274">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="a994b-274">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="a994b-275">*DataAccessLayerTest.cs* 包含 DAL 的單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-275">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="a994b-276">*IndexPageTests.cs* 包含索引頁面模型的單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-276">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="a994b-277">*公用程式*</span><span class="sxs-lookup"><span data-stu-id="a994b-277">*Utilities*</span></span>     | <span data-ttu-id="a994b-278">包含 `TestDbContextOptions` 用來為每個 DAL 單元測試建立新資料庫內容選項的方法，以便將資料庫重設為每個測試的基準條件。</span><span class="sxs-lookup"><span data-stu-id="a994b-278">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="a994b-279">測試架構為 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="a994b-279">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="a994b-280">物件模擬架構為 [Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="a994b-280">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="a994b-281">資料存取層 (DAL 的單元測試) </span><span class="sxs-lookup"><span data-stu-id="a994b-281">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="a994b-282">訊息應用程式具有具有四個包含在類別中的方法 `AppDbContext` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-282">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="a994b-283">每個方法在測試應用程式中都有一個或兩個單元測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-283">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="a994b-284">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="a994b-284">DAL method</span></span>               | <span data-ttu-id="a994b-285">函式</span><span class="sxs-lookup"><span data-stu-id="a994b-285">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="a994b-286">從資料庫取得，並 `List<Message>` 依屬性排序 `Text` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-286">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="a994b-287">將加入 `Message` 至資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-287">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="a994b-288">`Message`從資料庫中刪除所有專案。</span><span class="sxs-lookup"><span data-stu-id="a994b-288">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="a994b-289">`Message`從資料庫中刪除單一 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-289">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="a994b-290"><xref:Microsoft.EntityFrameworkCore.DbContextOptions>為每個測試建立新的時，DAL 需要單元測試 `AppDbContext` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-290">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="a994b-291">針對每個測試建立的其中一種方法 `DbContextOptions` 是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="a994b-291">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="a994b-292">這種方法的問題在於，每個測試都會以先前測試所用的任何狀態來接收資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-292">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="a994b-293">當您嘗試撰寫不會干擾彼此的不可部分完成單元測試時，這可能會造成問題。</span><span class="sxs-lookup"><span data-stu-id="a994b-293">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="a994b-294">若要強制將 `AppDbContext` 新的資料庫內容用於每個測試，請提供以 `DbContextOptions` 新的服務提供者為基礎的實例。</span><span class="sxs-lookup"><span data-stu-id="a994b-294">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="a994b-295">測試應用程式會示範如何使用其 `Utilities` class 方法 (test `TestDbContextOptions` */ Razor PagesTestSample. 測試/公用事業/utilities*) ：</span><span class="sxs-lookup"><span data-stu-id="a994b-295">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="a994b-296">`DbContextOptions`在 DAL 單元測試中使用，可讓每個測試以不可部分完成的方式以全新的資料庫實例執行：</span><span class="sxs-lookup"><span data-stu-id="a994b-296">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="a994b-297">類別中的每個測試方法 `DataAccessLayerTest` (*>--run-unittests/DataAccessLayerTest*) 遵循類似的排列-Act 判斷提示模式：</span><span class="sxs-lookup"><span data-stu-id="a994b-297">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="a994b-298">排列：資料庫設定為測試及/或定義預期的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-298">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="a994b-299">Act：執行測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-299">Act: The test is executed.</span></span>
1. <span data-ttu-id="a994b-300">判斷提示：判斷測試結果是否成功的判斷提示。</span><span class="sxs-lookup"><span data-stu-id="a994b-300">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="a994b-301">例如， `DeleteMessageAsync` 方法會負責移除其 `Id` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 所識別的單一訊息：</span><span class="sxs-lookup"><span data-stu-id="a994b-301">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="a994b-302">這種方法有兩種測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-302">There are two tests for this method.</span></span> <span data-ttu-id="a994b-303">當訊息存在於資料庫中時，一項測試會檢查此方法是否會刪除訊息。</span><span class="sxs-lookup"><span data-stu-id="a994b-303">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="a994b-304">另一種方法會測試如果刪除的訊息不存在，資料庫不會變更 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-304">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="a994b-305">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="a994b-305">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="a994b-306">首先，此方法會執行「排列」步驟，以進行「動作」步驟的準備。</span><span class="sxs-lookup"><span data-stu-id="a994b-306">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="a994b-307">系統會取得並保存植入訊息 `seedMessages` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-307">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="a994b-308">植入訊息會儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="a994b-308">The seeding messages are saved into the database.</span></span> <span data-ttu-id="a994b-309">具有 `Id` 之的訊息 `1` 會設定為 [刪除]。</span><span class="sxs-lookup"><span data-stu-id="a994b-309">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="a994b-310">當 `DeleteMessageAsync` 執行方法時，預期的訊息應該會有所有的訊息，但不包括包含的訊息 `Id` `1` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-310">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="a994b-311">`expectedMessages`變數表示此預期的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-311">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="a994b-312">方法 `DeleteMessageAsync` 會執行：方法會傳遞的 `recId` `1` ：</span><span class="sxs-lookup"><span data-stu-id="a994b-312">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="a994b-313">最後，方法 `Messages` 會從內容中取得，並將它與判斷提示兩者相等的判斷提示比較 `expectedMessages` ：</span><span class="sxs-lookup"><span data-stu-id="a994b-313">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="a994b-314">為了比較兩者 `List<Message>` 相同：</span><span class="sxs-lookup"><span data-stu-id="a994b-314">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="a994b-315">訊息的排序依據 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-315">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="a994b-316">訊息配對會在屬性上進行比較 `Text` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-316">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="a994b-317">類似的測試方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 會檢查嘗試刪除不存在之訊息的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-317">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="a994b-318">在此情況下，資料庫中的預期訊息應該會在執行方法之後等於實際的訊息 `DeleteMessageAsync` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-318">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="a994b-319">資料庫的內容應該不會有任何變更：</span><span class="sxs-lookup"><span data-stu-id="a994b-319">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="a994b-320">頁面模型方法的單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-320">Unit tests of the page model methods</span></span>

<span data-ttu-id="a994b-321">另一組單元測試負責測試頁面模型方法。</span><span class="sxs-lookup"><span data-stu-id="a994b-321">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="a994b-322">在訊息應用程式中，索引頁面模型可在 `IndexModel` *src/ Razor PagesTestSample/Pages/Index. .cs*的類別中找到。</span><span class="sxs-lookup"><span data-stu-id="a994b-322">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="a994b-323">頁面模型方法</span><span class="sxs-lookup"><span data-stu-id="a994b-323">Page model method</span></span> | <span data-ttu-id="a994b-324">函式</span><span class="sxs-lookup"><span data-stu-id="a994b-324">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="a994b-325">使用方法，從 DAL 取得適用于 UI 的訊息 `GetMessagesAsync` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-325">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="a994b-326">如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，則呼叫會 `AddMessageAsync` 將訊息加入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-326">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="a994b-327">呼叫 `DeleteAllMessagesAsync` 以刪除資料庫中的所有訊息。</span><span class="sxs-lookup"><span data-stu-id="a994b-327">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="a994b-328">執行 `DeleteMessageAsync` 以刪除具有指定之的訊息 `Id` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-328">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="a994b-329">如果資料庫中有一或多個訊息，則會計算每個訊息的平均單字數。</span><span class="sxs-lookup"><span data-stu-id="a994b-329">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="a994b-330">頁面模型方法會使用類別中的七個測試進行測試 `IndexPageTests` (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) 。</span><span class="sxs-lookup"><span data-stu-id="a994b-330">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="a994b-331">測試會使用熟悉的「排列-判斷提示-Act」模式。</span><span class="sxs-lookup"><span data-stu-id="a994b-331">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="a994b-332">這些測試著重于：</span><span class="sxs-lookup"><span data-stu-id="a994b-332">These tests focus on:</span></span>

* <span data-ttu-id="a994b-333">判斷在 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 無效時，方法是否遵循正確的行為。</span><span class="sxs-lookup"><span data-stu-id="a994b-333">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="a994b-334">確認方法會產生正確的 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 。</span><span class="sxs-lookup"><span data-stu-id="a994b-334">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="a994b-335">檢查是否已正確設定屬性值。</span><span class="sxs-lookup"><span data-stu-id="a994b-335">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="a994b-336">這組測試通常會模擬 DAL 的方法，為執行頁面模型方法的 Act 步驟產生預期的資料。</span><span class="sxs-lookup"><span data-stu-id="a994b-336">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="a994b-337">例如，的 `GetMessagesAsync` 方法 `AppDbContext` 會模擬以產生輸出。</span><span class="sxs-lookup"><span data-stu-id="a994b-337">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="a994b-338">當頁面模型方法執行此方法時，mock 會傳回結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-338">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="a994b-339">資料不是來自資料庫。</span><span class="sxs-lookup"><span data-stu-id="a994b-339">The data doesn't come from the database.</span></span> <span data-ttu-id="a994b-340">這會建立可預測、可靠的測試條件，以便在頁面模型測試中使用 DAL。</span><span class="sxs-lookup"><span data-stu-id="a994b-340">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="a994b-341">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`測試會顯示如何 `GetMessagesAsync` 針對頁面模型模擬方法：</span><span class="sxs-lookup"><span data-stu-id="a994b-341">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="a994b-342">當 `OnGetAsync` 方法在 Act 步驟中執行時，它會呼叫頁面模型的 `GetMessagesAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="a994b-342">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="a994b-343">單元測試動作步驟 (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="a994b-343">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="a994b-344">`IndexPage` 頁面模型的 `OnGetAsync` 方法 (*src/ Razor PagesTestSample/Pages/Index. cshtml*) ：</span><span class="sxs-lookup"><span data-stu-id="a994b-344">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="a994b-345">`GetMessagesAsync`DAL 中的方法不會傳回此方法呼叫的結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-345">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="a994b-346">方法的模擬版本會傳回結果。</span><span class="sxs-lookup"><span data-stu-id="a994b-346">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="a994b-347">在 `Assert` 步驟中， () 的實際訊息 `actualMessages` 是從 `Messages` 頁面模型的屬性指派。</span><span class="sxs-lookup"><span data-stu-id="a994b-347">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="a994b-348">當指派訊息時，也會執行類型檢查。</span><span class="sxs-lookup"><span data-stu-id="a994b-348">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="a994b-349">預期和實際的訊息會以其屬性進行比較 `Text` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-349">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="a994b-350">測試會判斷兩個 `List<Message>` 實例是否包含相同的訊息。</span><span class="sxs-lookup"><span data-stu-id="a994b-350">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="a994b-351">此群組中的其他測試會建立頁面模型物件，這些物件包含 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> 、、 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> ， <xref:Microsoft.AspNetCore.Mvc.ActionContext> 以建立 `PageContext` 、 `ViewDataDictionary` 和 `PageContext` 。</span><span class="sxs-lookup"><span data-stu-id="a994b-351">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="a994b-352">這些都適用于進行測試。</span><span class="sxs-lookup"><span data-stu-id="a994b-352">These are useful in conducting tests.</span></span> <span data-ttu-id="a994b-353">例如，訊息應用程式 `ModelState` 會建立錯誤， <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 以檢查 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 執行時是否傳回有效的 `OnPostAddMessageAsync` ：</span><span class="sxs-lookup"><span data-stu-id="a994b-353">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="a994b-354">其他資源</span><span class="sxs-lookup"><span data-stu-id="a994b-354">Additional resources</span></span>

* [<span data-ttu-id="a994b-355">使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試</span><span class="sxs-lookup"><span data-stu-id="a994b-355">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="a994b-356">[對程式碼進行單元測試](/visualstudio/test/unit-test-your-code) (Visual Studio) </span><span class="sxs-lookup"><span data-stu-id="a994b-356">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="a994b-357">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="a994b-357">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="a994b-358">使用 Visual Studio for Mac 在 macOS 上建置完整的 .NET Core 方案</span><span class="sxs-lookup"><span data-stu-id="a994b-358">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="a994b-359">開始使用 xUnit.net：搭配使用 .NET Core 與 .NET SDK 命令列</span><span class="sxs-lookup"><span data-stu-id="a994b-359">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="a994b-360">Moq</span><span class="sxs-lookup"><span data-stu-id="a994b-360">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="a994b-361">Moq 快速入門</span><span class="sxs-lookup"><span data-stu-id="a994b-361">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
* <span data-ttu-id="a994b-362">[JustMockLite](https://github.com/telerik/JustMockLite)：適用于 .net 開發人員的模擬架構。</span><span class="sxs-lookup"><span data-stu-id="a994b-362">[JustMockLite](https://github.com/telerik/JustMockLite): A mocking framework for .NET developers.</span></span> <span data-ttu-id="a994b-363"> (*不會受到 Microsoft 的維護或支援*) </span><span class="sxs-lookup"><span data-stu-id="a994b-363">(*Not maintained or supported by Microsoft.*)</span></span>

::: moniker-end
