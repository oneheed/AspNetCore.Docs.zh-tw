---
title: ASP.NET Core Blazor 資料系結
author: guardrex
description: 瞭解應用程式中的元件和檔物件模型 (DOM) 元素的資料系結功能 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/15/2021
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
uid: blazor/components/data-binding
ms.openlocfilehash: 5f1e9963a7b62b90ee492bebe9bfc357a8090caf
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105051032"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor 資料系結

Razor 元件提供具有 [`@bind`](xref:mvc/views/razor#bind) Razor 欄位、屬性或運算式值之指示詞屬性的資料系結功能 Razor 。

下列範例會系結：

* `<input>`C # 欄位的元素值 `inputValue` 。
* C # 屬性的第二個 `<input>` 元素值 `InputValue` 。

當 `<input>` 元素失去焦點時，就會更新其系結欄位或屬性。

`Pages/Bind.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

只有在轉譯元件時，才會更新 UI 中的文字方塊，而不是回應變更欄位或屬性的值。 由於元件會在事件處理常式程式碼執行之後自行轉譯，因此在觸發事件處理常式之後，欄位和屬性更新通常會立即反映在 UI 中。

下列範例示範如何在 HTML 中撰寫資料系結，並將屬性系結 `InputValue` 至第二個 `<input>` 元素的 `value` 和 [`onchange`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange) 屬性。 *下列範例中的第二個 `<input>` 元素是概念示範，並非建議您如何在元件中系結資料 Razor 。*

`Pages/BindTheory.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

轉譯 `BindTheory` 元件時， `value` HTML 示範元素的會 `<input>` 來自 `InputValue` 屬性。 當使用者在文字方塊中輸入值並變更元素焦點時， `onchange` 就會引發事件，並將 `InputValue` 屬性設定為變更的值。 事實上，程式碼執行較複雜，因為它會 [`@bind`](xref:mvc/views/razor#bind) 處理型別轉換的執行情況。 一般來說，會將 [`@bind`](xref:mvc/views/razor#bind) 運算式的目前值與屬性產生關聯 `value` ，並使用已註冊的處理常式來處理變更。

將屬性或欄位系結到其他 [檔物件模型 (dom) ](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件，方法是包含 `@bind:event="{EVENT}"` 包含預留位置之 DOM 事件的屬性 `{EVENT}` 。 下列範例 `InputValue` `<input>` 會在觸發元素的[ `oninput` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput)時，將屬性系結至專案的值。 不同于元素失去焦點時所引發的[ `onchange` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange)， [`oninput`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) 當文字方塊的值變更時，就會引發。

`Page/BindEvent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

Razor 屬性系結會區分大小寫：

* `@bind` 和 `@bind:event` 都有效。
* `@Bind`/`@Bind:Event` (大寫字母 `B` 和 `E`) 或 `@BIND` / `@BIND:EVENT` (所有大寫字母) **都無效**。

## <a name="unparsable-values"></a>無法剖析的值

當使用者提供無法剖析的值給資料系結專案時，會在觸發 bind 事件時，將無法剖析的值自動還原為先前的值。

請考慮下列元件，其中 `<input>` 元素會系結至 `int` 具有初始值的型別 `123` 。

`Pages/UnparsableValues.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

根據預設，系結會套用至元素的 `onchange` 事件。 如果使用者將文字方塊的專案值更新為 `123.45` 並變更焦點，則在引發時，元素的值會還原為 `123` `onchange` 。 當值 `123.45` 被拒絕時，若要改用的原始值 `123` ，使用者瞭解其值不會被接受。

針對 `oninput` () 的事件 `@bind:event="oninput"` ，回復值會在導入未剖析值的任何按鍵之後發生。 以具有系結類型的事件為目標時 `oninput` `int` ，使用者無法輸入點 (`.`) 字元。 `.`立即移除) 字元 (的點，讓使用者收到只允許整數的立即回應。 在某些情況下，還原事件的值 `oninput` 並不理想，例如，當使用者應該清除無法剖析的 `<input>` 值時。 替代方案包括：

* 請勿使用 `oninput` 事件。 使用預設 `onchange` 事件，在此情況下，將不會還原無效值，直到專案失去焦點為止。
* 系結至可為 null 的型別（例如或）， `int?` `string` 並提供 [自訂 `get` 和 `set` 存取子邏輯](#custom-binding-formats) 來處理不正確專案。
* 使用 [表單驗證元件](xref:blazor/forms-validation)，例如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 。 表單驗證元件提供內建支援來管理不正確輸入。 表單驗證元件：
  * 允許使用者提供不正確輸入，並在相關聯的上接收驗證錯誤 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。
  * 在 UI 中顯示驗證錯誤，而不會干擾使用者輸入其他 webform 資料。

## <a name="format-strings"></a>格式字串

資料系結 <xref:System.DateTime> 會使用單一格式字串 `@bind:format="{FORMAT STRING}"` ，其中 `{FORMAT STRING}` 預留位置是格式字串。 其他格式運算式（例如貨幣或數位格式）目前無法使用，但可能會在未來的版本中加入。

`Pages/DateBinding.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

在上述程式碼中， `<input>` 元素的欄位類型 (`type` 屬性) 預設為 `text` 。

可為 null <xref:System.DateTime?displayProperty=fullName> 且 <xref:System.DateTimeOffset?displayProperty=fullName> 受支援：

```csharp
private DateTime? date;
private DateTimeOffset? dateOffset;
```

`date`由於 Blazor 具有格式化日期的內建支援，因此不建議指定欄位類型的格式。 在建議的情況下， `yyyy-MM-dd` 如果格式是以欄位類型提供，只使用日期格式讓系結正常運作 `date` ：

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="custom-binding-formats"></a>自訂系結格式

[C # `get` 和 `set` 存取](/dotnet/csharp/programming-guide/classes-and-structs/using-properties) 子可以用來建立自訂系結格式行為，如下列元件所示 `DecimalBinding` 。 元件 pesudo 會透過 `<input>` `string` 屬性 () ，將一個或多個小數位數的十進位或負數值系結至元素 `DecimalValue` 。

`Pages/DecimalBinding.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

## <a name="binding-with-component-parameters"></a>使用元件參數進行系結

常見的案例是將子元件的屬性系結至其父元件中的屬性。 此案例稱為 *連鎖* 系結，因為有多個層級的系結同時發生。

[元件參數](xref:blazor/components/index#component-parameters) 允許父元件的系結屬性與 `@bind-{PROPERTY}` 語法，其中 `{PROPERTY}` 預留位置是要系結的屬性。

您無法使用子元件中的語法來執行連結系結 [`@bind`](xref:mvc/views/razor#bind) 。 必須個別指定事件處理常式和值，以支援從子元件的父系更新屬性。

父元件仍會利用 [`@bind`](xref:mvc/views/razor#bind) 語法來設定與子元件的資料系結。

下列 `ChildBind` 元件具有 `Year` 元件參數和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 。 依照慣例， <xref:Microsoft.AspNetCore.Components.EventCallback%601> 參數的必須以 "" 尾碼命名為元件參數名稱 `Changed` 。 命名語法是 `{PARAMETER NAME}Changed` ，其中 `{PARAMETER NAME}` 預留位置是參數名稱。 在下列範例中， <xref:Microsoft.AspNetCore.Components.EventCallback%601> 名為 `YearChanged` 。

<xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 使用提供的引數叫用與系結相關聯的委派，並分派已變更屬性的事件通知。

`Shared/ChildBind.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

如需事件和的詳細資訊 <xref:Microsoft.AspNetCore.Components.EventCallback%601> ，請參閱本文的 *>app >eventcallback* 一節 <xref:blazor/components/event-handling#eventcallback> 。

在下列 `Parent` 元件中， `year` 欄位會系結至 `Year` 子元件的參數。 `Year`參數是可系結的，因為它具有 `YearChanged` 符合參數類型的伴隨事件 `Year` 。

`Pages/Parent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

依照慣例，屬性可以系結至對應的事件處理常式，其方式 `@bind-{PROPERTY}:event` 是將指派給處理常式的屬性（attribute），其中 `{PROPERTY}` 預留位置是屬性（property）。 `<ChildBind @bind-Year="year" />` 相當於撰寫：

```razor
<ChildBind @bind-Year="year" @bind-Year:event="YearChanged" />
```

在更複雜的真實世界範例中，下列 `Password` 元件：

* 將 `<input>` 元素的值設定為 `password` 欄位。
* 將屬性的變更公開 `Password` 至父代元件 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) ，該元件會傳入子系欄位的目前值 `password` 做為其引數。
* 使用 `onclick` 事件來觸發 `ToggleShowPassword` 方法。 如需詳細資訊，請參閱<xref:blazor/components/event-handling>。

`Shared/PasswordEntry.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

`PasswordEntry`元件是在另一個元件中使用，例如下列 `PasswordBinding` 元件範例。

`Pages/PasswordBinding.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

在叫用系結委派的方法中執行檢查或陷阱錯誤。 下列修訂的 `PasswordEntry` 元件會在密碼的值中使用空格時，為使用者提供立即的意見反應。

`Shared/PasswordEntry.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

## <a name="bind-across-more-than-two-components"></a>跨兩個以上的元件進行系結

您可以透過任意數目的嵌套元件來系結參數，但您必須遵循單向資料流程：

* 變更通知會在階層中 *往上流動*。
* 新的參數值會在階層中 *往下流動*。

常見和建議的方法是只將基礎資料儲存在父元件中，以避免任何必須更新狀態的混淆，如下列範例所示。

`Pages/Parent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

`Shared/NestedChild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

`Shared/NestedGrandchild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

如需適合用來在記憶體中共用資料的替代方法，以及跨不一定要進行嵌套的元件，請參閱 <xref:blazor/state-management> 。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/forms-validation>
* [系結至表單中的選項按鈕](xref:blazor/forms-validation#radio-buttons)
* [將 `<select>` 元件選項系結至 `null` 表單中的 c # 物件值](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
* [ASP.NET Core Blazor 事件處理： `EventCallback` 區段](xref:blazor/components/event-handling#eventcallback)
