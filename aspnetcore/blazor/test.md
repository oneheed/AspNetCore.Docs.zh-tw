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
# <a name="test-components-in-aspnet-core-no-locblazor"></a>ASP.NET Core 中的測試元件 Blazor

[Egil Hansen](https://egilhansen.com/)

測試是建立穩定和可維護軟體的重要層面。

若要測試 Blazor 元件， *受測試的元件* (剪下) 是：

* 以測試的相關輸入呈現。
* 視所執行的測試類型而定，可能會受到互動或修改。 例如，可以觸發事件處理常式，例如 `onclick` 按鈕的事件。
* 檢查是否有預期的值。

## <a name="test-approaches"></a>測試方法

測試元件的兩個常見方法 Blazor 是端對端 (E2E) 測試和單元測試：

* **單元測試**： [單元測試](/dotnet/core/testing/) 是以提供下列內容的單元測試程式庫撰寫：
  * 元件轉譯。
  * 檢查元件輸出和狀態。
  * 觸發事件處理常式和生命週期方法。
  * 元件行為正確的判斷提示。

  [bUnit](https://github.com/egil/bUnit) 是可啟用元件單元測試的程式庫範例 Razor 。

* **E2E 測試**：測試執行器會執行 Blazor 應用程式，其中包含剪下並自動執行瀏覽器實例。 測試控管會透過瀏覽器檢查並與剪下互動。 [Selenium](https://github.com/SeleniumHQ/selenium) 是可搭配應用程式使用的 E2E 測試架構範例 Blazor 。

在單元測試中，只 Blazor 涉及 (Razor /c # ) 的元件。 外部相依性（例如服務和 JS interop）必須是模擬。 在 E2E 測試中， Blazor 元件和其所有輔助基礎結構都是測試的一部分，包括 CSS、JS 和 DOM 和瀏覽器 api。

*測試範圍* 會描述測試的廣泛程度。 測試範圍通常會影響測試的速度。 單元測試會在應用程式子系統的子集上執行，而且通常會以毫秒來執行。 E2E 測試：測試應用程式子系統的廣泛群組，可能需要幾秒鐘的時間才能完成。 

單元測試也可以存取剪下的實例，以允許檢查和驗證元件的內部狀態。 這通常無法在 E2E 測試中進行。

相對於元件的環境，E2E 測試必須確定已達到預期的環境狀態，然後才會開始驗證。 否則，結果會是無法預測的。 在單元測試中，測試剪下和生命週期的呈現更為整合，這可改善測試穩定性。

E2E 測試牽涉到啟動多個進程、網路和磁片 i/o，以及其他經常導致測試可靠性不佳的子系統活動。 單元測試通常不會受到這類問題的隔離。

下表摘要說明這兩種測試方法之間的差異。

| 功能                       | 單元測試                     | E2E 測試                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| 測試範圍                       | Blazor 元件 (Razor /c # 僅 )  | BlazorRazor具有 CSS/JS 的元件 (/c # )  |
| 測試回合時間              | 毫秒                     | 秒                                 |
| 元件實例的存取權 | 是                              | 否                                      |
| 對環境的敏感性     | 否                               | 是                                     |
| 可靠性                      | 更可靠                    | 較不可靠                           |

## <a name="choose-the-most-appropriate-test-approach"></a>選擇最適當的測試方法

選擇要執行的測試類型時，請考慮這種情況。 下表說明一些考慮。

| 狀況 | 建議的方法 | 備註 |
| -------- | ------------------ | ------- |
| 沒有 JS interop 邏輯的元件 | 單元測試 | 當元件中沒有 JS interop 的相依性時 Blazor ，可以在不存取 js 或 DOM API 的情況下測試元件。 在此案例中，選擇單元測試沒有缺點。 |
| 具有簡單 JS interop 邏輯的元件 | 單元測試 | 元件通常是透過 JS interop 來查詢 DOM 或觸發動畫。 在此案例中，通常會偏好進行單元測試，因為透過介面模擬 JS 互動十分簡單 <xref:Microsoft.JSInterop.IJSRuntime> 。 |
| 相依于複雜 JS 程式碼的元件 | 單元測試和獨立的 JS 測試 | 如果元件使用 JS interop 呼叫大型或複雜的 JS 程式庫 Blazor ，但元件和 js 程式庫之間的互動很簡單，那麼最好的方法可能會將元件和 js 程式庫或程式碼視為兩個不同的部分，並個別進行測試。 Blazor使用單元測試程式庫測試元件，並使用 js 測試程式庫測試 js。 |
| 具有相依于瀏覽器 DOM 之 JS 操作的邏輯元件 | E2E 測試 | 當元件的功能相依于 JS 並操作 DOM 時，請在 E2E 測試中同時驗證 JS 和 Blazor 程式碼。 這是 Blazor 架構開發人員使用 Blazor 的瀏覽器呈現邏輯所採用的方法，其具有緊密結合的 c # 和 JS 程式碼。 C # 和 JS 程式碼必須一起運作，才能 Blazor 在瀏覽器中正確呈現元件。
| 相依于協力廠商元件程式庫並具有硬式模擬相關性的元件 | E2E 測試 | 當元件的功能相依于具有難以模擬之相依性的協力廠商元件程式庫（例如 JS interop）時，E2E 測試可能是測試元件的唯一選項。 |

## <a name="test-components-with-bunit"></a>使用 bUnit 測試元件

沒有適用于的官方 Microsoft 測試架構 Blazor ，但以社區為導向的專案 [bUnit](https://github.com/egil/bUnit) 提供便利的方式來進行單元測試 Blazor 元件。

> [!NOTE]
> bUnit 是協力廠商測試程式庫，並不受 Microsoft 支援或維護。

bUnit 適用于一般用途的測試架構，例如 [MSTest](/dotnet/core/testing/unit-testing-with-mstest)、 [NUnit](https://nunit.org/)和 [xUnit](https://xunit.github.io/)。 這些測試架構讓 bUnit 測試看起來像平常的單元測試。 與一般用途測試架構整合的 bUnit 測試，通常會以執行：

* [Visual Studio 的測試瀏覽器](/visualstudio/test/run-unit-tests-with-test-explorer)。
* [`dotnet test`](/dotnet/core/tools/dotnet-test) 命令 shell 中的 CLI 命令。
* 自動化的 DevOps 測試管線。

> [!NOTE]
> 跨不同測試架構的測試概念和測試執行類似，但不完全相同。 如需指導方針，請參閱測試架構的檔。

下列範例會根據 `Counter` 專案範本，示範應用程式中元件的 bUnit 測試結構 Blazor 。 `Counter`元件會根據使用者選取頁面中的按鈕，顯示並遞增計數器：

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

下列 bUnit 測試會在選取按鈕時，確認剪下的計數器是否已正確遞增：

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

下列動作會在測試的每個步驟中進行：

* *排列*： `Counter` 元件會使用 bUnit 的來呈現 `TestContext` 。 已找到剪下的段落元素 (`<p>`) ，並將其指派給 `paraElm` 。

* *Act*： () 中的按鈕元素，然後藉 `<button>` 由呼叫來加以選取 `Click` ，這應該會遞增計數器，並更新 () 之段落標記的內容 `<p>` 。 藉由呼叫來取得段落元素文字內容 `TextContent` 。

* *Assert*： `MarkupMatches` 在文字內容上呼叫，以確認它符合預期的字串，也就是 `Current count: 1` 。

> [!NOTE]
> `MarkupMatches`Assert 方法與一般字串比較判斷提示不同 (例如，) 會 `Assert.Equal("Current count: 1", paraElmText);` `MarkupMatches` 執行輸入和預期 HTML 標籤的語義比較。 語義比較會感知 HTML 語義，這表示會忽略不重要的空白字元之類的專案。 這會導致更穩定的測試。 如需詳細資訊，請參閱 [自訂語義 HTML 比較](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)。

## <a name="additional-resources"></a>其他資源

* [具有 bUnit 的消費者入門](https://bunit.egilhansen.com/docs/getting-started/)： bUnit 指示包含建立測試專案、參考測試架構封裝，以及建立和執行測試的指引。
