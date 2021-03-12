---
title: Razor ASP.NET Core 中的頁面單元測試
author: rick-anderson
description: 瞭解如何為 Razor 頁面應用程式建立單元測試。
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
uid: test/razor-pages-tests
ms.openlocfilehash: a67f51561d0a165db04418f2040ffe68e3ebe8ce
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588486"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor ASP.NET Core 中的頁面單元測試

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 支援 Razor 頁面應用程式的單元測試。 資料存取層 (DAL) 和頁面模型的測試有助於確保：

* RazorPages 應用程式的各個部分在應用程式結構期間，會獨立地以一個單位來運作。
* 類別和方法的責任範圍有限。
* 應用程式的行為方式有其他檔。
* 在自動化的建立和部署期間，會發現回歸（這是程式碼更新所帶來的錯誤）。

本主題假設您對 Razor 頁面應用程式和單元測試有基本的瞭解。 如果您不熟悉 Razor 頁面應用程式或測試概念，請參閱下列主題：

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/razor-pages-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

範例專案是由兩個應用程式所組成：

| 應用程式         | 專案資料夾                     | Description |
| ----------- | ---------------------------------- | ----------- |
| 訊息應用程式 | *src/ Razor PagesTestSample*         | 可讓使用者新增訊息、刪除一則訊息、刪除所有訊息，以及分析訊息 (尋找每個訊息的平均單字數目) 。 |
| 測試應用程式    | *測試/ Razor PagesTestSample* | 用來對訊息應用程式的 DAL 和索引頁面模型進行單元測試。 |

您可以使用 IDE 的內建測試功能來執行測試，例如 [Visual studio](/visualstudio/test/unit-test-your-code) 或 [Visual studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)。 如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesTestSample* ] 資料夾的命令提示字元中執行下列命令：

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>訊息應用程式組織

訊息應用程式是 Razor 具有下列特性的頁面訊息系統：

* 應用程式的 [索引] 頁面 (*Pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法來控制訊息的新增、刪除和分析， (尋找每個訊息) 的平均單字數。
* 訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。 `Text`屬性是必要的，且限制為200個字元。
* 訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/) 來儲存&#8224;。
* 應用程式在其資料庫內容類別中包含 DAL， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。 DAL 方法會標示為 `virtual` ，可讓您模擬要在測試中使用的方法。
* 如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。 這些 *植* 入的訊息也會在測試中使用。

&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。 本主題使用 [xUnit](https://xunit.github.io/) 測試架構。 跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。

雖然範例應用程式不會使用存放庫模式，且不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。 如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 (範例中的實 <xref:mvc/controllers/testing> 作為儲存機制模式) 。

## <a name="test-app-organization"></a>測試應用程式組織

測試應用程式是 [ *測試/ Razor PagesTestSample* ] 資料夾內的主控台應用程式。

| 測試應用程式資料夾 | Description |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 包含 DAL 的單元測試。</li><li>*IndexPageTests.cs* 包含索引頁面模型的單元測試。</li></ul> |
| *公用程式*     | 包含 `TestDbContextOptions` 用來為每個 DAL 單元測試建立新資料庫內容選項的方法，以便將資料庫重設為每個測試的基準條件。 |

測試架構為 [xUnit](https://xunit.github.io/)。 物件模擬架構為 [Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>資料存取層 (DAL 的單元測試) 

訊息應用程式具有具有四個包含在類別中的方法 `AppDbContext` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 。 每個方法在測試應用程式中都有一個或兩個單元測試。

| DAL 方法               | 函式                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 從資料庫取得，並 `List<Message>` 依屬性排序 `Text` 。 |
| `AddMessageAsync`        | 將加入 `Message` 至資料庫。                                          |
| `DeleteAllMessagesAsync` | `Message`從資料庫中刪除所有專案。                           |
| `DeleteMessageAsync`     | `Message`從資料庫中刪除單一 `Id` 。                      |

<xref:Microsoft.EntityFrameworkCore.DbContextOptions>為每個測試建立新的時，DAL 需要單元測試 `AppDbContext` 。 針對每個測試建立的其中一種方法 `DbContextOptions` 是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> ：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

這種方法的問題在於，每個測試都會以先前測試所用的任何狀態來接收資料庫。 當您嘗試撰寫不會干擾彼此的不可部分完成單元測試時，這可能會造成問題。 若要強制將 `AppDbContext` 新的資料庫內容用於每個測試，請提供以 `DbContextOptions` 新的服務提供者為基礎的實例。 測試應用程式會示範如何使用其 `Utilities` class 方法 (test `TestDbContextOptions` */ Razor PagesTestSample. 測試/公用事業/utilities*) ：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

`DbContextOptions`在 DAL 單元測試中使用，可讓每個測試以不可部分完成的方式以全新的資料庫實例執行：

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

類別中的每個測試方法 `DataAccessLayerTest` (*>--run-unittests/DataAccessLayerTest*) 遵循類似的排列-Act 判斷提示模式：

1. 排列：資料庫設定為測試及/或定義預期的結果。
1. Act：執行測試。
1. 判斷提示：判斷測試結果是否成功的判斷提示。

例如， `DeleteMessageAsync` 方法會負責移除其 `Id` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 所識別的單一訊息：

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

這種方法有兩種測試。 當訊息存在於資料庫中時，一項測試會檢查此方法是否會刪除訊息。 另一種方法會測試如果刪除的訊息不存在，資料庫不會變更 `Id` 。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，此方法會執行「排列」步驟，以進行「動作」步驟的準備。 系統會取得並保存植入訊息 `seedMessages` 。 植入訊息會儲存在資料庫中。 具有 `Id` 之的訊息 `1` 會設定為 [刪除]。 當 `DeleteMessageAsync` 執行方法時，預期的訊息應該會有所有的訊息，但不包括包含的訊息 `Id` `1` 。 `expectedMessages`變數表示此預期的結果。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

方法 `DeleteMessageAsync` 會執行：方法會傳遞的 `recId` `1` ：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最後，方法 `Messages` 會從內容中取得，並將它與判斷提示兩者相等的判斷提示比較 `expectedMessages` ：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

為了比較兩者 `List<Message>` 相同：

* 訊息的排序依據 `Id` 。
* 訊息配對會在屬性上進行比較 `Text` 。

類似的測試方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 會檢查嘗試刪除不存在之訊息的結果。 在此情況下，資料庫中的預期訊息應該會在執行方法之後等於實際的訊息 `DeleteMessageAsync` 。 資料庫的內容應該不會有任何變更：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>頁面模型方法的單元測試

另一組單元測試負責測試頁面模型方法。 在訊息應用程式中，索引頁面模型可在 `IndexModel` *src/ Razor PagesTestSample/Pages/Index. .cs* 的類別中找到。

| 頁面模型方法 | 函式 |
| ----------------- | -------- |
| `OnGetAsync` | 使用方法，從 DAL 取得適用于 UI 的訊息 `GetMessagesAsync` 。 |
| `OnPostAddMessageAsync` | 如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，則呼叫會 `AddMessageAsync` 將訊息加入至資料庫。 |
| `OnPostDeleteAllMessagesAsync` | 呼叫 `DeleteAllMessagesAsync` 以刪除資料庫中的所有訊息。 |
| `OnPostDeleteMessageAsync` | 執行 `DeleteMessageAsync` 以刪除具有指定之的訊息 `Id` 。 |
| `OnPostAnalyzeMessagesAsync` | 如果資料庫中有一或多個訊息，則會計算每個訊息的平均單字數。 |

頁面模型方法會使用類別中的七個測試進行測試 `IndexPageTests` (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) 。 測試會使用熟悉的「排列-判斷提示-Act」模式。 這些測試著重于：

* 判斷在 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 無效時，方法是否遵循正確的行為。
* 確認方法會產生正確的 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 。
* 檢查是否已正確設定屬性值。

這組測試通常會模擬 DAL 的方法，為執行頁面模型方法的 Act 步驟產生預期的資料。 例如，的 `GetMessagesAsync` 方法 `AppDbContext` 會模擬以產生輸出。 當頁面模型方法執行此方法時，mock 會傳回結果。 資料不是來自資料庫。 這會建立可預測、可靠的測試條件，以便在頁面模型測試中使用 DAL。

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`測試會顯示如何 `GetMessagesAsync` 針對頁面模型模擬方法：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

當 `OnGetAsync` 方法在 Act 步驟中執行時，它會呼叫頁面模型的 `GetMessagesAsync` 方法。

單元測試動作步驟 (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) ：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 頁面模型的 `OnGetAsync` 方法 (*src/ Razor PagesTestSample/Pages/Index. cshtml*) ：

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`DAL 中的方法不會傳回此方法呼叫的結果。 方法的模擬版本會傳回結果。

在 `Assert` 步驟中， () 的實際訊息 `actualMessages` 是從 `Messages` 頁面模型的屬性指派。 當指派訊息時，也會執行類型檢查。 預期和實際的訊息會以其屬性進行比較 `Text` 。 測試會判斷兩個 `List<Message>` 實例是否包含相同的訊息。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此群組中的其他測試會建立頁面模型物件，這些物件包含 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> 、、 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> ， <xref:Microsoft.AspNetCore.Mvc.ActionContext> 以建立 `PageContext` 、 `ViewDataDictionary` 和 `PageContext` 。 這些都適用于進行測試。 例如，訊息應用程式 `ModelState` 會建立錯誤， <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 以檢查 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 執行時是否傳回有效的 `OnPostAddMessageAsync` ：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他資源

* [使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [對程式碼進行單元測試](/visualstudio/test/unit-test-your-code) (Visual Studio) 
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [使用 Visual Studio for Mac 在 macOS 上建置完整的 .NET Core 方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [開始使用 xUnit.net：搭配使用 .NET Core 與 .NET SDK 命令列](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 快速入門](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 支援 Razor 頁面應用程式的單元測試。 資料存取層 (DAL) 和頁面模型的測試有助於確保：

* RazorPages 應用程式的各個部分在應用程式結構期間，會獨立地以一個單位來運作。
* 類別和方法的責任範圍有限。
* 應用程式的行為方式有其他檔。
* 在自動化的建立和部署期間，會發現回歸（這是程式碼更新所帶來的錯誤）。

本主題假設您對 Razor 頁面應用程式和單元測試有基本的瞭解。 如果您不熟悉 Razor 頁面應用程式或測試概念，請參閱下列主題：

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/test/razor-pages-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

範例專案是由兩個應用程式所組成：

| 應用程式         | 專案資料夾                     | Description |
| ----------- | ---------------------------------- | ----------- |
| 訊息應用程式 | *src/ Razor PagesTestSample*         | 可讓使用者新增訊息、刪除一則訊息、刪除所有訊息，以及分析訊息 (尋找每個訊息的平均單字數目) 。 |
| 測試應用程式    | *測試/ Razor PagesTestSample* | 用來對訊息應用程式的 DAL 和索引頁面模型進行單元測試。 |

您可以使用 IDE 的內建測試功能來執行測試，例如 [Visual studio](/visualstudio/test/unit-test-your-code) 或 [Visual studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)。 如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesTestSample* ] 資料夾的命令提示字元中執行下列命令：

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>訊息應用程式組織

訊息應用程式是 Razor 具有下列特性的頁面訊息系統：

* 應用程式的 [索引] 頁面 (*Pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法來控制訊息的新增、刪除和分析， (尋找每個訊息) 的平均單字數。
* 訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。 `Text`屬性是必要的，且限制為200個字元。
* 訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/) 來儲存&#8224;。
* 應用程式在其資料庫內容類別中包含 DAL， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。 DAL 方法會標示為 `virtual` ，可讓您模擬要在測試中使用的方法。
* 如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。 這些 *植* 入的訊息也會在測試中使用。

&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。 本主題使用 [xUnit](https://xunit.github.io/) 測試架構。 跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。

雖然範例應用程式不會使用存放庫模式，且不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。 如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 (範例中的實 <xref:mvc/controllers/testing> 作為儲存機制模式) 。

## <a name="test-app-organization"></a>測試應用程式組織

測試應用程式是 [ *測試/ Razor PagesTestSample* ] 資料夾內的主控台應用程式。

| 測試應用程式資料夾 | Description |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 包含 DAL 的單元測試。</li><li>*IndexPageTests.cs* 包含索引頁面模型的單元測試。</li></ul> |
| *公用程式*     | 包含 `TestDbContextOptions` 用來為每個 DAL 單元測試建立新資料庫內容選項的方法，以便將資料庫重設為每個測試的基準條件。 |

測試架構為 [xUnit](https://xunit.github.io/)。 物件模擬架構為 [Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>資料存取層 (DAL 的單元測試) 

訊息應用程式具有具有四個包含在類別中的方法 `AppDbContext` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 。 每個方法在測試應用程式中都有一個或兩個單元測試。

| DAL 方法               | 函式                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 從資料庫取得，並 `List<Message>` 依屬性排序 `Text` 。 |
| `AddMessageAsync`        | 將加入 `Message` 至資料庫。                                          |
| `DeleteAllMessagesAsync` | `Message`從資料庫中刪除所有專案。                           |
| `DeleteMessageAsync`     | `Message`從資料庫中刪除單一 `Id` 。                      |

<xref:Microsoft.EntityFrameworkCore.DbContextOptions>為每個測試建立新的時，DAL 需要單元測試 `AppDbContext` 。 針對每個測試建立的其中一種方法 `DbContextOptions` 是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> ：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

這種方法的問題在於，每個測試都會以先前測試所用的任何狀態來接收資料庫。 當您嘗試撰寫不會干擾彼此的不可部分完成單元測試時，這可能會造成問題。 若要強制將 `AppDbContext` 新的資料庫內容用於每個測試，請提供以 `DbContextOptions` 新的服務提供者為基礎的實例。 測試應用程式會示範如何使用其 `Utilities` class 方法 (test `TestDbContextOptions` */ Razor PagesTestSample. 測試/公用事業/utilities*) ：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

`DbContextOptions`在 DAL 單元測試中使用，可讓每個測試以不可部分完成的方式以全新的資料庫實例執行：

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

類別中的每個測試方法 `DataAccessLayerTest` (*>--run-unittests/DataAccessLayerTest*) 遵循類似的排列-Act 判斷提示模式：

1. 排列：資料庫設定為測試及/或定義預期的結果。
1. Act：執行測試。
1. 判斷提示：判斷測試結果是否成功的判斷提示。

例如， `DeleteMessageAsync` 方法會負責移除其 `Id` (*src/ Razor PagesTestSample/Data/AppDbCoNtext*) 所識別的單一訊息：

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

這種方法有兩種測試。 當訊息存在於資料庫中時，一項測試會檢查此方法是否會刪除訊息。 另一種方法會測試如果刪除的訊息不存在，資料庫不會變更 `Id` 。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，此方法會執行「排列」步驟，以進行「動作」步驟的準備。 系統會取得並保存植入訊息 `seedMessages` 。 植入訊息會儲存在資料庫中。 具有 `Id` 之的訊息 `1` 會設定為 [刪除]。 當 `DeleteMessageAsync` 執行方法時，預期的訊息應該會有所有的訊息，但不包括包含的訊息 `Id` `1` 。 `expectedMessages`變數表示此預期的結果。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

方法 `DeleteMessageAsync` 會執行：方法會傳遞的 `recId` `1` ：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最後，方法 `Messages` 會從內容中取得，並將它與判斷提示兩者相等的判斷提示比較 `expectedMessages` ：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

為了比較兩者 `List<Message>` 相同：

* 訊息的排序依據 `Id` 。
* 訊息配對會在屬性上進行比較 `Text` 。

類似的測試方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 會檢查嘗試刪除不存在之訊息的結果。 在此情況下，資料庫中的預期訊息應該會在執行方法之後等於實際的訊息 `DeleteMessageAsync` 。 資料庫的內容應該不會有任何變更：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>頁面模型方法的單元測試

另一組單元測試負責測試頁面模型方法。 在訊息應用程式中，索引頁面模型可在 `IndexModel` *src/ Razor PagesTestSample/Pages/Index. .cs* 的類別中找到。

| 頁面模型方法 | 函式 |
| ----------------- | -------- |
| `OnGetAsync` | 使用方法，從 DAL 取得適用于 UI 的訊息 `GetMessagesAsync` 。 |
| `OnPostAddMessageAsync` | 如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，則呼叫會 `AddMessageAsync` 將訊息加入至資料庫。 |
| `OnPostDeleteAllMessagesAsync` | 呼叫 `DeleteAllMessagesAsync` 以刪除資料庫中的所有訊息。 |
| `OnPostDeleteMessageAsync` | 執行 `DeleteMessageAsync` 以刪除具有指定之的訊息 `Id` 。 |
| `OnPostAnalyzeMessagesAsync` | 如果資料庫中有一或多個訊息，則會計算每個訊息的平均單字數。 |

頁面模型方法會使用類別中的七個測試進行測試 `IndexPageTests` (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) 。 測試會使用熟悉的「排列-判斷提示-Act」模式。 這些測試著重于：

* 判斷在 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 無效時，方法是否遵循正確的行為。
* 確認方法會產生正確的 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 。
* 檢查是否已正確設定屬性值。

這組測試通常會模擬 DAL 的方法，為執行頁面模型方法的 Act 步驟產生預期的資料。 例如，的 `GetMessagesAsync` 方法 `AppDbContext` 會模擬以產生輸出。 當頁面模型方法執行此方法時，mock 會傳回結果。 資料不是來自資料庫。 這會建立可預測、可靠的測試條件，以便在頁面模型測試中使用 DAL。

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`測試會顯示如何 `GetMessagesAsync` 針對頁面模型模擬方法：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

當 `OnGetAsync` 方法在 Act 步驟中執行時，它會呼叫頁面模型的 `GetMessagesAsync` 方法。

單元測試動作步驟 (*測試/ Razor PagesTestSample. 測試/>--run-unittests/IndexPageTests .cs*) ：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 頁面模型的 `OnGetAsync` 方法 (*src/ Razor PagesTestSample/Pages/Index. cshtml*) ：

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`DAL 中的方法不會傳回此方法呼叫的結果。 方法的模擬版本會傳回結果。

在 `Assert` 步驟中， () 的實際訊息 `actualMessages` 是從 `Messages` 頁面模型的屬性指派。 當指派訊息時，也會執行類型檢查。 預期和實際的訊息會以其屬性進行比較 `Text` 。 測試會判斷兩個 `List<Message>` 實例是否包含相同的訊息。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此群組中的其他測試會建立頁面模型物件，這些物件包含 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> 、、 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> ， <xref:Microsoft.AspNetCore.Mvc.ActionContext> 以建立 `PageContext` 、 `ViewDataDictionary` 和 `PageContext` 。 這些都適用于進行測試。 例如，訊息應用程式 `ModelState` 會建立錯誤， <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 以檢查 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 執行時是否傳回有效的 `OnPostAddMessageAsync` ：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他資源

* [使用 dotnet test 與 xUnit 為 .NET Core 中的 C# 進行單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [對程式碼進行單元測試](/visualstudio/test/unit-test-your-code) (Visual Studio) 
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [使用 Visual Studio for Mac 在 macOS 上建置完整的 .NET Core 方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [開始使用 xUnit.net：搭配使用 .NET Core 與 .NET SDK 命令列](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 快速入門](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite](https://github.com/telerik/JustMockLite)：適用于 .net 開發人員的模擬架構。  (*不會受到 Microsoft 的維護或支援*) 

::: moniker-end
