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
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core 的 Blazor advanced 案例

## <a name="manual-rendertreebuilder-logic"></a>手動 RenderTreeBuilder 邏輯

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供操作元件和元素的方法，包括以 c # 程式碼手動建立元件。

> [!WARNING]
> 使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 來建立元件是一個 *advanced 案例*。 格式不正確的元件 (例如，未封閉的標記標記) 可能會導致未定義的行為。 未定義的行為包括中斷的內容轉譯、應用程式功能遺失，以及 **_洩漏的安全性_**。

請考慮下列 `PetDetails` 元件，該元件可以在另一個元件中手動呈現。

`Shared/PetDetails.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

在下列 `BuiltContent` 元件中，方法中的迴圈會 `CreateComponent` 產生三個 `PetDetails` 元件。

在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 具有序號的方法中，序號是源程式碼號。 Blazor差異演算法依賴不同行程式碼（而不是不同的呼叫調用）對應的序號。 使用方法建立元件時 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ，請將序號的引數硬式編碼。 **使用計算或計數器來產生序號可能會導致效能不佳。** 如需詳細資訊，請參閱序號與程式 [程式碼號相關，而不是執行順序](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) 一節。

`Pages/BuiltContent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

> [!WARNING]
> 中的類型 <xref:Microsoft.AspNetCore.Components.RenderTree> 允許處理轉譯作業的 *結果* 。 這些是架構執行的內部詳細資料 Blazor 。 這些類型應該視為不 *穩定* ，未來的版本可能會變更。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>序號與程式程式碼號和非執行順序相關

Razor 元件檔 (`.razor`) 一律會進行編譯。 執行已編譯的程式碼對解讀程式碼有潛在的優勢，因為產生已編譯器代碼的編譯步驟可以用來插入資訊，以改善執行時間的應用程式效能。

這些改進的主要範例包含 *序號*。 序號會向執行時間指出輸出來自哪些不同和已排序的程式程式碼。 執行時間會使用這項資訊，以線性時間產生有效率的樹狀結構差異，這遠比一般樹狀結構差異演算法一般可能更快。

請考慮下列 Razor 元件檔 (`.razor`) ：

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

上述的 Razor 標記和文字內容會編譯成 c # 程式碼，如下所示：

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

第一次執行程式碼時，產生器 `someFlag` 會 `true` 收到：

| 順序 | 類型      | 資料   |
| :------: | --------- | :----: |
| 0        | Text node | First  |
| 1        | Text node | Second |

想像這 `someFlag` 會變成 `false` ，並再次轉譯標記。 這次，產生器會收到：

| 順序 | 類型       | 資料   |
| :------: | ---------- | :----: |
| 1        | Text node  | Second |

當執行時間執行差異時，會看到順序中的專案已 `0` 移除，因此它會使用單一步驟產生下列簡單的 *編輯腳本* ：

* 移除第一個文位元組點。

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>以程式設計方式產生序號的問題

想像一下，您應該改為撰寫下列轉譯樹產生器邏輯：

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

現在，第一個輸出是：

| 順序 | 類型      | 資料   |
| :------: | --------- | :----: |
| 0        | Text node | First  |
| 1        | Text node | Second |

此結果與先前的案例相同，因此不會有負面問題。 `someFlag` 是 `false` 在第二個轉譯上，輸出為：

| 順序 | 類型      | 資料   |
| :------: | --------- | ------ |
| 0        | Text node | Second |

這次，diff 演算法會看到 *兩個* 變更已發生。 此演算法會產生下列編輯腳本：

* 將第一個文位元組點的值變更為 `Second` 。
* 移除第二個文位元組點。

產生序號已遺失有關 `if/else` 原始程式碼中的分支和迴圈出現位置的所有實用資訊。 這會導致差異 **兩次（只要** 前面）。

這是一個簡單的範例。 在更實際的案例中，如果是複雜且深層的嵌套結構，特別是迴圈，則效能成本通常較高。 差異演算法必須在轉譯樹狀結構中進行深度遞迴，而不是立即識別已插入或移除的迴圈區塊或分支。 這通常會導致建立較長的編輯腳本，因為 diff 演算法 misinformed 了舊的和新的結構彼此之間的關聯性。

### <a name="guidance-and-conclusions"></a>指引和結論

* 如果序號是動態產生的，應用程式效能就會受到影響。
* 架構無法在執行時間自動建立自己的序號，因為必要的資訊不存在，除非它是在編譯時期進行捕捉。
* 請勿撰寫很長的手動執行 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 邏輯區塊。 偏好使用檔案 `.razor` ，並讓編譯器處理序號。 如果您無法避免手動 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 邏輯，請將很長的程式碼區塊分割成包裝在呼叫中的較小部分 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 。 每個區域都有自己的序號不同的空間，因此您可以從零 (或在每個區域內) 任何其他任意數目的任一數字重新開機。
* 如果序號是硬式編碼，則差異演算法只會要求序號增加值。 初始值與間距無關。 其中一個合法的選項是使用程式程式碼號作為序號，或從零開始，並依一個或數百個 (或任何偏好的間隔) 來增加。
* Blazor 使用序號，其他樹狀結構比較的 UI 架構則不會使用它們。 使用序號時，比較會更快，且 Blazor 具有可自動處理開發人員撰寫檔案之序號的編譯步驟 `.razor` 。
