---
title: ASP.NET Core 中的測試元件 Blazor
author: guardrex
description: 瞭解如何在應用程式中測試 componments Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 1a7b1114934f4fe7006d60bdbd0f06792d2c6935
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394547"
---
# <a name="test-components-in-aspnet-core-blazor"></a>ASP.NET Core 中的測試元件 Blazor

依據： [Egil Hansen](https://egilhansen.com/)

測試是建立穩定和可維護軟體的重要層面。

若要測試 Blazor 元件，受測的元件 (剪 *下*) 為：

* 以測試的相關輸入來呈現。
* 視執行的測試類型而定，可能會受互動或修改。 例如，事件處理常式可以觸發，例如 `onclick` 按鈕的事件。
* 檢查是否有預期的值。

## <a name="test-approaches"></a>測試方法

測試元件的兩個常見方法 Blazor 是端對端 (E2E) 測試和單元測試：

* **單元測試**： [單元](/dotnet/core/testing/) 測試會使用提供下列提供的單元測試程式庫撰寫：
  * 元件呈現。
  * 檢查元件輸出和狀態。
  * 觸發事件處理常式和生命週期方法。
  * 元件行為正確的判斷提示。

  [bUnit](https://github.com/egil/bUnit) 是可啟用元件單元測試的程式庫範例 Razor 。

* **E2E 測試**：測試執行器會執行 Blazor 包含剪下的應用程式，並將瀏覽器實例自動化。 測試控管會檢查並透過瀏覽器與剪下互動。 [Selenium](https://github.com/SeleniumHQ/selenium) 是可搭配應用程式使用的 E2E 測試架構範例 Blazor 。

在單元測試中，只 Blazor 涉及 (Razor /c # ) 元件。 外部相依性（例如服務和 JS interop）必須是模擬。 在 E2E 測試中， Blazor 元件和其所有輔助基礎結構都是測試的一部分，包括 CSS、JS 以及 DOM 和瀏覽器 api。

*測試範圍* 描述測試的範圍。 測試範圍通常會影響測試的速度。 單元測試會在應用程式子系統的子集上執行，而且通常會以毫秒為單位執行。 測試廣泛應用程式子系統群組的 E2E 測試可能需要幾秒鐘的時間才能完成。 

單元測試也提供剪下實例的存取權，讓您檢查和驗證元件的內部狀態。 這通常無法在 E2E 測試中進行。

至於元件的環境，E2E 測試必須確保在驗證開始之前已達到預期的環境狀態。 否則，結果會無法預測。 在單元測試中，測試的剪下和生命週期的轉譯更為整合，可改善測試穩定性。

E2E 測試牽涉到啟動多個進程、網路和磁片 i/o，以及通常會導致測試可靠性不良的其他子系統活動。 單元測試通常會與這些種類的問題隔離。

下表摘要說明這兩種測試方法之間的差異。

| 功能                       | 單元測試                     | E2E 測試                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| 測試範圍                       | Blazor 元件 (Razor /c # 僅 )  | Blazor 元件 (Razor /c # ) 搭配 CSS/JS |
| 測試回合時間              | 毫秒                     | 秒                                 |
| 元件實例的存取權 | 是                              | 否                                      |
| 對環境的敏感性     | 否                               | 是                                     |
| 可靠性                      | 更可靠                    | 較不可靠                           |

## <a name="choose-the-most-appropriate-test-approach"></a>選擇最適當的測試方法

選擇要執行的測試類型時，請考慮這個案例。 下表說明一些考慮。

| 狀況 | 建議的方法 | 備註 |
| -------- | ------------------ | ------- |
| 沒有 JS interop 邏輯的元件 | 單元測試 | 當元件中沒有 JS interop 的相依性時 Blazor ，可以測試元件，而不需要存取 js 或 DOM API。 在此案例中，選擇單元測試沒有缺點。 |
| 具有簡單 JS interop 邏輯的元件 | 單元測試 | 元件通常是透過 JS interop 來查詢 DOM 或觸發動畫。 在此案例中，通常偏好單元測試，因為它可以直接透過介面模擬 JS 互動 <xref:Microsoft.JSInterop.IJSRuntime> 。 |
| 相依于複雜 JS 程式碼的元件 | 單元測試和個別的 JS 測試 | 如果元件使用 JS interop 來呼叫大型或複雜的 JS 程式庫 Blazor ，但元件和 js 程式庫之間的互動很簡單，那麼最好的方法就是將元件和 js 程式庫或程式碼視為兩個不同的部分，並個別測試。 Blazor使用單元測試程式庫測試元件，並使用 js 測試程式庫測試 js。 |
| 邏輯相依于瀏覽器 DOM 之 JS 操作的元件 | E2E 測試 | 當元件的功能相依于 JS 以及其對 DOM 的操作時，請 Blazor 在 E2E 測試中同時確認 JS 和程式碼。 這是 Blazor framework 開發人員使用的瀏覽器轉譯邏輯所採用的方法 Blazor ，其具有緊密結合的 c # 和 JS 程式碼。 C # 和 JS 程式碼必須一起運作，才能 Blazor 在瀏覽器中正確呈現元件。
| 相依于具有硬式相依性之協力廠商元件程式庫的元件 | E2E 測試 | 當元件的功能相依于具有難以模擬相依性的協力廠商元件庫時（例如 JS interop），E2E 測試可能是測試元件的唯一選項。 |

## <a name="test-components-with-bunit"></a>使用 bUnit 測試元件

沒有適用的官方 Microsoft 測試架構 Blazor ，但 [社區驅動專案 [bUnit](https://github.com/egil/bUnit) ] 提供一個便利的方式來單元測試 Blazor 元件。

> [!NOTE]
> bUnit 是協力廠商測試程式庫，Microsoft 不支援或維護。

bUnit 適用于一般用途的測試架構，例如 [MSTest](/dotnet/core/testing/unit-testing-with-mstest)、 [NUnit](https://nunit.org/)和 [xUnit](https://xunit.github.io/)。 這些測試架構讓 bUnit 測試看起來和覺得像是一般單元測試。 與一般用途的測試架構整合的 bUnit 測試通常會以下列方式執行：

* [Visual Studio 的 Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer)。
* [`dotnet test`](/dotnet/core/tools/dotnet-test) 命令 shell 中的 CLI 命令。
* 自動化的 DevOps 測試管線。

> [!NOTE]
> 跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。 請參閱測試架構的檔以取得指導方針。

以下示範以 `Counter` [ Blazor 專案範本](xref:blazor/project-structure)為基礎之應用程式中元件的 bUnit 測試結構。 `Counter`元件會根據使用者選取頁面中的按鈕，顯示並遞增計數器：

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

下列 bUnit 測試會在選取按鈕時，確認剪下的計數器是否已正確地遞增：

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

下列動作會在測試的每個步驟進行：

* *排列*：此 `Counter` 元件是使用 bUnit 的呈現 `TestContext` 。 會找到剪下的段落專案 (`<p>`) ，並將其指派給 `paraElm` 。

* *Act*：按鈕的元素 (`<button>`) ，然後藉由呼叫來選取 `Click` ，這應該會遞增計數器，並將段落標記的內容更新 (`<p>`) 。 您可以藉由呼叫來取得段落元素文字內容 `TextContent` 。

* *Assert*： `MarkupMatches` 會在文字內容上呼叫，以確認它符合預期的字串，也就是 `Current count: 1` 。

> [!NOTE]
> `MarkupMatches`Assert 方法與一般字串比較判斷提示不同 (例如，) 會 `Assert.Equal("Current count: 1", paraElmText);` `MarkupMatches` 執行輸入和預期 HTML 標籤的語法比較。 語義比較可感知 HTML 語義，這表示會忽略不必要的空白字元。 這會導致更穩定的測試。 如需詳細資訊，請參閱 [自訂語義 HTML 比較](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)。

## <a name="additional-resources"></a>其他資源

* [開始使用 bUnit](https://bunit.egilhansen.com/docs/getting-started/)： bUnit 指示包含有關建立測試專案、參考測試架構封裝，以及建立和執行測試的指引。
