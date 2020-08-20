---
title: ASP.NET Core Blazor 資料系結
author: guardrex
description: 瞭解應用程式中元件和 DOM 元素的資料系結功能 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628556"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="8bfa0-103">ASP.NET Core Blazor 資料系結</span><span class="sxs-lookup"><span data-stu-id="8bfa0-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="8bfa0-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8bfa0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8bfa0-105">Razor 元件會透過以 [`@bind`](xref:mvc/views/razor#bind) 欄位、屬性或運算式值命名的 HTML 元素屬性，提供資料系結功能 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="8bfa0-106">下列範例會將專案系結 `<input>` 至 `currentValue` 欄位，並將元素系結 `<input>` 至 `CurrentValue` 屬性：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8bfa0-107">當其中一個元素失去焦點時，就會更新其系結欄位或屬性。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="8bfa0-108">只有在轉譯元件時，才會更新 UI 中的文字方塊，而不是回應變更欄位或屬性的值。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="8bfa0-109">由於元件會在事件處理常式程式碼執行之後自行轉譯，因此在觸發事件處理常式之後，欄位和屬性更新 *通常* 會立即反映在 UI 中。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="8bfa0-110">使用 [`@bind`](xref:mvc/views/razor#bind) 與 `CurrentValue` 屬性 (`<input @bind="CurrentValue" />`) 基本上等同于下列專案：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8bfa0-111">轉譯元件時， `value` 輸入元素的會來自 `CurrentValue` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="8bfa0-112">當使用者在文字方塊中輸入並變更元素焦點時， `onchange` 就會引發事件，並將 `CurrentValue` 屬性設定為變更的值。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="8bfa0-113">事實上，程式碼產生比起的複雜，因為它會 [`@bind`](xref:mvc/views/razor#bind) 處理型別轉換的執行情況。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="8bfa0-114">在主體中，會將 [`@bind`](xref:mvc/views/razor#bind) 運算式的目前值與屬性產生關聯 `value` ，並使用已註冊的處理常式來處理變更。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="8bfa0-115">將屬性或欄位系結到其他事件，也可以包含 `@bind:event` 具有參數的屬性 `event` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="8bfa0-116">下列範例會系結 `CurrentValue` 事件上的屬性 `oninput` ：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8bfa0-117">不同于 `onchange` 當專案失去焦點時引發的專案，會 `oninput` 在文字方塊的值變更時引發。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="8bfa0-118">使用 `@bind-{ATTRIBUTE}` with 語法來系結以外的 `@bind-{ATTRIBUTE}:event` 元素屬性 `value` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="8bfa0-119">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="8bfa0-119">In the following example:</span></span>

* <span data-ttu-id="8bfa0-120">當元件載入 () 時，段落的樣式為 **紅色** `style="color:red"` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="8bfa0-121">使用者變更文字方塊的值，以反映不同的 CSS 色彩樣式，並變更頁面的元素焦點。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="8bfa0-122">例如，使用者將文字方塊值變更為 `color:blue` ，然後按下鍵盤上的 <kbd>Tab</kbd> 鍵。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="8bfa0-123">當元素焦點變更時：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-123">When the element focus changes:</span></span>
  * <span data-ttu-id="8bfa0-124">的值 `paragraphStyle` 是從專案 `<input>` 的值指派。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="8bfa0-125">段落樣式會更新，以反映中的新樣式 `paragraphStyle` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="8bfa0-126">如果樣式更新為 `color:blue` ，則文字色彩會變更為 **藍色**。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="8bfa0-127">屬性系結會區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="8bfa0-128">`@bind` 有效。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-128">`@bind` is valid.</span></span>
* <span data-ttu-id="8bfa0-129">`@Bind` 和 `@BIND` 無效。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="8bfa0-130">無法剖析的值</span><span class="sxs-lookup"><span data-stu-id="8bfa0-130">Unparsable values</span></span>

<span data-ttu-id="8bfa0-131">當使用者提供無法剖析的值給資料系結專案時，會在觸發 bind 事件時，將無法剖析的值自動還原為先前的值。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="8bfa0-132">請考慮下列案例：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-132">Consider the following scenario:</span></span>

* <span data-ttu-id="8bfa0-133">專案 `<input>` 會系結至 `int` 值為的類型 `123` ：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="8bfa0-134">使用者會將專案的值更新為 `123.45` 頁面中的，並變更元素焦點。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="8bfa0-135">在上述案例中，元素的值會還原為 `123` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="8bfa0-136">當值 `123.45` 被拒絕時，若要改用的原始值 `123` ，使用者瞭解其值不會被接受。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="8bfa0-137">根據預設，系結會套用至元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`) 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="8bfa0-138">用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 來觸發不同事件的系結。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="8bfa0-139">針對 `oninput` () 的事件 `@bind:event="oninput"` ，回復會在引進未剖析值的任何按鍵之後進行。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="8bfa0-140">以系結類型為目標的 `oninput` 事件時 `int` ，使用者無法輸入 `.` 字元。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="8bfa0-141">`.`系統會立即移除字元，因此使用者會收到只允許整數的立即回應。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="8bfa0-142">在某些情況下，還原事件的值 `oninput` 並不理想，例如，當使用者應該清除無法剖析的 `<input>` 值時。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="8bfa0-143">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-143">Alternatives include:</span></span>

* <span data-ttu-id="8bfa0-144">請勿使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="8bfa0-145">使用預設 `onchange` 事件 (只指定 `@bind="{PROPERTY OR FIELD}"`) ，其中不會還原無效值，直到元素失去焦點為止。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="8bfa0-146">系結至可為 null 的型別（例如或）， `int?` `string` 並提供自訂邏輯來處理不正確專案。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="8bfa0-147">使用 [表單驗證元件](xref:blazor/forms-validation)，例如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="8bfa0-148">表單驗證元件具有內建的支援，可管理不正確輸入。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="8bfa0-149">如需詳細資訊，請參閱<xref:blazor/forms-validation>。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="8bfa0-150">表單驗證元件：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-150">Form validation components:</span></span>
  * <span data-ttu-id="8bfa0-151">允許使用者提供不正確輸入，並在相關聯的上接收驗證錯誤 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="8bfa0-152">在 UI 中顯示驗證錯誤，而不會干擾使用者輸入其他 webform 資料。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="8bfa0-153">格式字串</span><span class="sxs-lookup"><span data-stu-id="8bfa0-153">Format strings</span></span>

<span data-ttu-id="8bfa0-154">資料系結 <xref:System.DateTime> 使用的格式字串 `@bind:format` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="8bfa0-155">現在無法使用其他格式運算式，例如貨幣或數位格式。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="8bfa0-156">在上述程式碼中， `<input>` 元素的欄位類型 (`type`) 預設為 `text` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="8bfa0-157">`@bind:format` 支援系結下列 .NET 類型：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="8bfa0-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="8bfa0-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="8bfa0-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="8bfa0-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="8bfa0-160">`@bind:format`屬性會指定要套用至元素之的日期格式 `value` `<input>` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="8bfa0-161">當事件發生時，也會使用此格式來剖析該值 `onchange` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="8bfa0-162">`date`由於 Blazor 具有格式化日期的內建支援，因此不建議指定欄位類型的格式。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="8bfa0-163">在建議的情況下， `yyyy-MM-dd` 如果格式是以欄位類型提供，只使用日期格式讓系結正常運作 `date` ：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="8bfa0-164">使用元件參數的父系對子系結</span><span class="sxs-lookup"><span data-stu-id="8bfa0-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="8bfa0-165">元件參數允許父元件的系結屬性和欄位具有 `@bind-{PROPERTY OR FIELD}` 語法。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="8bfa0-166">下列 `Child` 元件 (`Child.razor`) 具有 `Year` 元件參數和 `YearChanged` 回呼：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="8bfa0-167">回呼 (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) 必須命名為元件參數名稱，後面接著 " `Changed` " 尾碼 (`{PARAMETER NAME}Changed`) 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="8bfa0-168">在上述範例中，回呼的名稱為 `YearChanged` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="8bfa0-169">如需 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 的詳細資訊，請參閱 <xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="8bfa0-170">在下列 `Parent` 元件 (`Parent.razor`) 中，欄位會系結 `year` 至 `Year` 子元件的參數：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="8bfa0-171">`Year`參數是可系結的，因為它具有 `YearChanged` 符合參數類型的伴隨事件 `Year` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="8bfa0-172">依照慣例，屬性可以藉由包含指派給處理常式的屬性，系結至對應的事件處理常式 `@bind-{PROPERTY}:event` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="8bfa0-173">`<Child @bind-Year="year" />` 相當於撰寫：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="8bfa0-174">具有連結系結的子系對父系系結</span><span class="sxs-lookup"><span data-stu-id="8bfa0-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="8bfa0-175">常見的案例是將資料系結參數連結至元件輸出中的頁面元素。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="8bfa0-176">此案例稱為 *連鎖* 系結，因為有多個層級的系結同時發生。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="8bfa0-177">連結系結無法使用 [`@bind`](xref:mvc/views/razor#bind) 子元件中的語法來執行。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="8bfa0-178">您必須分別指定事件處理常式和值。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="8bfa0-179">不過，父元件可以搭配 [`@bind`](xref:mvc/views/razor#bind) 子元件的參數使用語法。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="8bfa0-180">下列 `PasswordField` 元件 (`PasswordField.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="8bfa0-181">將 `<input>` 元素的值設定為 `Password` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="8bfa0-182">`Password`使用來公開屬性對父元件的變更 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="8bfa0-183">使用 `onclick` 事件來觸發 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="8bfa0-184">如需詳細資訊，請參閱<xref:blazor/components/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="8bfa0-185">`PasswordField`元件是在另一個元件中使用：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="8bfa0-186">若要對上述範例中的密碼執行檢查或陷阱錯誤：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="8bfa0-187">`Password` `password` 在下列範例程式碼) 中，建立 (的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="8bfa0-188">在 setter 中執行檢查或陷阱錯誤 `Password` 。</span><span class="sxs-lookup"><span data-stu-id="8bfa0-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="8bfa0-189">下列範例會在密碼值中使用空格時，為使用者提供立即的意見反應：</span><span class="sxs-lookup"><span data-stu-id="8bfa0-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="8bfa0-190">其他資源</span><span class="sxs-lookup"><span data-stu-id="8bfa0-190">Additional resources</span></span>

* [<span data-ttu-id="8bfa0-191">系結至表單中的選項按鈕</span><span class="sxs-lookup"><span data-stu-id="8bfa0-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="8bfa0-192">將 `<select>` 元件選項系結至 `null` 表單中的 c # 物件值</span><span class="sxs-lookup"><span data-stu-id="8bfa0-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
