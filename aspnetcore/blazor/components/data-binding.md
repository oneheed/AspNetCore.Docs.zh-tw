---
title: 'ASP.NET Core :::no-loc(Blazor)::: 資料系結'
author: guardrex
description: '瞭解應用程式中元件和 DOM 元素的資料系結功能 :::no-loc(Blazor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/data-binding
ms.openlocfilehash: f1730ed366fc81444ffe54e88bcd33147efb0aa7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056292"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="056c7-103">ASP.NET Core :::no-loc(Blazor)::: 資料系結</span><span class="sxs-lookup"><span data-stu-id="056c7-103">ASP.NET Core :::no-loc(Blazor)::: data binding</span></span>

<span data-ttu-id="056c7-104">[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="056c7-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="056c7-105">:::no-loc(Razor)::: 元件會透過以 [`@bind`](xref:mvc/views/razor#bind) 欄位、屬性或運算式值命名的 HTML 元素屬性，提供資料系結功能 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="056c7-105">:::no-loc(Razor)::: components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or :::no-loc(Razor)::: expression value.</span></span>

<span data-ttu-id="056c7-106">下列範例會將專案系結 `<input>` 至 `currentValue` 欄位，並將元素系結 `<input>` 至 `CurrentValue` 屬性：</span><span class="sxs-lookup"><span data-stu-id="056c7-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="056c7-107">當其中一個元素失去焦點時，就會更新其系結欄位或屬性。</span><span class="sxs-lookup"><span data-stu-id="056c7-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="056c7-108">只有在轉譯元件時，才會更新 UI 中的文字方塊，而不是回應變更欄位或屬性的值。</span><span class="sxs-lookup"><span data-stu-id="056c7-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="056c7-109">由於元件會在事件處理常式程式碼執行之後自行轉譯，因此在觸發事件處理常式之後，欄位和屬性更新 *通常* 會立即反映在 UI 中。</span><span class="sxs-lookup"><span data-stu-id="056c7-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="056c7-110">使用 [`@bind`](xref:mvc/views/razor#bind) 與 `CurrentValue` 屬性 (`<input @bind="CurrentValue" />`) 基本上等同于下列專案：</span><span class="sxs-lookup"><span data-stu-id="056c7-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="056c7-111">轉譯元件時， `value` 輸入元素的會來自 `CurrentValue` 屬性。</span><span class="sxs-lookup"><span data-stu-id="056c7-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="056c7-112">當使用者在文字方塊中輸入並變更元素焦點時， `onchange` 就會引發事件，並將 `CurrentValue` 屬性設定為變更的值。</span><span class="sxs-lookup"><span data-stu-id="056c7-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="056c7-113">事實上，程式碼產生比起的複雜，因為它會 [`@bind`](xref:mvc/views/razor#bind) 處理型別轉換的執行情況。</span><span class="sxs-lookup"><span data-stu-id="056c7-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="056c7-114">在主體中，會將 [`@bind`](xref:mvc/views/razor#bind) 運算式的目前值與屬性產生關聯 `value` ，並使用已註冊的處理常式來處理變更。</span><span class="sxs-lookup"><span data-stu-id="056c7-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="056c7-115">將屬性或欄位系結到其他事件，也可以包含 `@bind:event` 具有參數的屬性 `event` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="056c7-116">下列範例會系結 `CurrentValue` 事件上的屬性 `oninput` ：</span><span class="sxs-lookup"><span data-stu-id="056c7-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="056c7-117">不同于 `onchange` 當專案失去焦點時引發的專案，會 `oninput` 在文字方塊的值變更時引發。</span><span class="sxs-lookup"><span data-stu-id="056c7-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="056c7-118">屬性系結會區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="056c7-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="056c7-119">`@bind` 有效。</span><span class="sxs-lookup"><span data-stu-id="056c7-119">`@bind` is valid.</span></span>
* <span data-ttu-id="056c7-120">`@Bind` 和 `@BIND` 無效。</span><span class="sxs-lookup"><span data-stu-id="056c7-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="056c7-121">無法剖析的值</span><span class="sxs-lookup"><span data-stu-id="056c7-121">Unparsable values</span></span>

<span data-ttu-id="056c7-122">當使用者提供無法剖析的值給資料系結專案時，會在觸發 bind 事件時，將無法剖析的值自動還原為先前的值。</span><span class="sxs-lookup"><span data-stu-id="056c7-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="056c7-123">考慮下列案例：</span><span class="sxs-lookup"><span data-stu-id="056c7-123">Consider the following scenario:</span></span>

* <span data-ttu-id="056c7-124">專案 `<input>` 會系結至 `int` 值為的類型 `123` ：</span><span class="sxs-lookup"><span data-stu-id="056c7-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="056c7-125">使用者會將專案的值更新為 `123.45` 頁面中的，並變更元素焦點。</span><span class="sxs-lookup"><span data-stu-id="056c7-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="056c7-126">在上述案例中，元素的值會還原為 `123` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="056c7-127">當值 `123.45` 被拒絕時，若要改用的原始值 `123` ，使用者瞭解其值不會被接受。</span><span class="sxs-lookup"><span data-stu-id="056c7-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="056c7-128">根據預設，系結會套用至元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`) 。</span><span class="sxs-lookup"><span data-stu-id="056c7-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="056c7-129">用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 來觸發不同事件的系結。</span><span class="sxs-lookup"><span data-stu-id="056c7-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="056c7-130">針對 `oninput` () 的事件 `@bind:event="oninput"` ，回復會在引進未剖析值的任何按鍵之後進行。</span><span class="sxs-lookup"><span data-stu-id="056c7-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="056c7-131">以系結類型為目標的 `oninput` 事件時 `int` ，使用者無法輸入 `.` 字元。</span><span class="sxs-lookup"><span data-stu-id="056c7-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="056c7-132">`.`系統會立即移除字元，因此使用者會收到只允許整數的立即回應。</span><span class="sxs-lookup"><span data-stu-id="056c7-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="056c7-133">在某些情況下，還原事件的值 `oninput` 並不理想，例如，當使用者應該清除無法剖析的 `<input>` 值時。</span><span class="sxs-lookup"><span data-stu-id="056c7-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="056c7-134">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="056c7-134">Alternatives include:</span></span>

* <span data-ttu-id="056c7-135">請勿使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="056c7-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="056c7-136">使用預設 `onchange` 事件 (只指定 `@bind="{PROPERTY OR FIELD}"`) ，其中不會還原無效值，直到元素失去焦點為止。</span><span class="sxs-lookup"><span data-stu-id="056c7-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="056c7-137">系結至可為 null 的型別（例如或）， `int?` `string` 並提供自訂邏輯來處理不正確專案。</span><span class="sxs-lookup"><span data-stu-id="056c7-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="056c7-138">使用 [表單驗證元件](xref:blazor/forms-validation)，例如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 。</span><span class="sxs-lookup"><span data-stu-id="056c7-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="056c7-139">表單驗證元件具有內建的支援，可管理不正確輸入。</span><span class="sxs-lookup"><span data-stu-id="056c7-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="056c7-140">如需詳細資訊，請參閱<xref:blazor/forms-validation>。</span><span class="sxs-lookup"><span data-stu-id="056c7-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="056c7-141">表單驗證元件：</span><span class="sxs-lookup"><span data-stu-id="056c7-141">Form validation components:</span></span>
  * <span data-ttu-id="056c7-142">允許使用者提供不正確輸入，並在相關聯的上接收驗證錯誤 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="056c7-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="056c7-143">在 UI 中顯示驗證錯誤，而不會干擾使用者輸入其他 webform 資料。</span><span class="sxs-lookup"><span data-stu-id="056c7-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="056c7-144">格式字串</span><span class="sxs-lookup"><span data-stu-id="056c7-144">Format strings</span></span>

<span data-ttu-id="056c7-145">資料系結 <xref:System.DateTime> 使用的格式字串 `@bind:format` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="056c7-146">現在無法使用其他格式運算式，例如貨幣或數位格式。</span><span class="sxs-lookup"><span data-stu-id="056c7-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="056c7-147">在上述程式碼中， `<input>` 元素的欄位類型 (`type`) 預設為 `text` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="056c7-148">`@bind:format` 支援系結下列 .NET 類型：</span><span class="sxs-lookup"><span data-stu-id="056c7-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="056c7-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="056c7-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="056c7-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="056c7-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="056c7-151">`@bind:format`屬性會指定要套用至元素之的日期格式 `value` `<input>` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="056c7-152">當事件發生時，也會使用此格式來剖析該值 `onchange` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="056c7-153">`date`由於 :::no-loc(Blazor)::: 具有格式化日期的內建支援，因此不建議指定欄位類型的格式。</span><span class="sxs-lookup"><span data-stu-id="056c7-153">Specifying a format for the `date` field type isn't recommended because :::no-loc(Blazor)::: has built-in support to format dates.</span></span> <span data-ttu-id="056c7-154">在建議的情況下， `yyyy-MM-dd` 如果格式是以欄位類型提供，只使用日期格式讓系結正常運作 `date` ：</span><span class="sxs-lookup"><span data-stu-id="056c7-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="056c7-155">使用元件參數進行系結</span><span class="sxs-lookup"><span data-stu-id="056c7-155">Binding with component parameters</span></span>

<span data-ttu-id="056c7-156">常見的案例是將子元件中的屬性系結至其父系中的屬性。</span><span class="sxs-lookup"><span data-stu-id="056c7-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="056c7-157">此案例稱為 *連鎖* 系結，因為有多個層級的系結同時發生。</span><span class="sxs-lookup"><span data-stu-id="056c7-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="056c7-158">元件參數允許父元件的系結屬性和欄位具有 `@bind-{PROPERTY OR FIELD}` 語法。</span><span class="sxs-lookup"><span data-stu-id="056c7-158">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="056c7-159">連結系結無法使用 [`@bind`](xref:mvc/views/razor#bind) 子元件中的語法來執行。</span><span class="sxs-lookup"><span data-stu-id="056c7-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="056c7-160">必須個別指定事件處理常式和值，以支援從子元件的父系更新屬性。</span><span class="sxs-lookup"><span data-stu-id="056c7-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="056c7-161">父元件仍會利用 [`@bind`](xref:mvc/views/razor#bind) 語法來設定子元件的資料系結。</span><span class="sxs-lookup"><span data-stu-id="056c7-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="056c7-162">下列 `Child` 元件 (`Shared/Child.razor`) 具有 `Year` 元件參數和 `YearChanged` 回呼：</span><span class="sxs-lookup"><span data-stu-id="056c7-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="056c7-163">回呼 (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) 必須命名為元件參數名稱，後面接著 " `Changed` " 尾碼 (`{PARAMETER NAME}Changed`) 。</span><span class="sxs-lookup"><span data-stu-id="056c7-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="056c7-164">在上述範例中，回呼的名稱為 `YearChanged` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="056c7-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 使用提供的引數叫用與系結相關聯的委派，並分派已變更屬性的事件通知。</span><span class="sxs-lookup"><span data-stu-id="056c7-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="056c7-166">在下列 `Parent` 元件 (`Parent.razor`) 中，欄位會系結 `year` 至 `Year` 子元件的參數：</span><span class="sxs-lookup"><span data-stu-id="056c7-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="056c7-167">`Year`參數是可系結的，因為它具有 `YearChanged` 符合參數類型的伴隨事件 `Year` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="056c7-168">依照慣例，屬性可以藉由包含指派給處理常式的屬性，系結至對應的事件處理常式 `@bind-{PROPERTY}:event` 。</span><span class="sxs-lookup"><span data-stu-id="056c7-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="056c7-169">`<Child @bind-Year="year" />` 相當於撰寫：</span><span class="sxs-lookup"><span data-stu-id="056c7-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="056c7-170">在更複雜的真實世界範例中，下列 `PasswordField` 元件 (`PasswordField.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="056c7-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="056c7-171">將 `<input>` 元素的值設定為 `password` 欄位。</span><span class="sxs-lookup"><span data-stu-id="056c7-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="056c7-172">將屬性的變更公開 `Password` 至父代元件 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) ，該元件會傳入子系欄位的目前值 `password` 做為其引數。</span><span class="sxs-lookup"><span data-stu-id="056c7-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="056c7-173">使用 `onclick` 事件來觸發 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="056c7-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="056c7-174">如需詳細資訊，請參閱<xref:blazor/components/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="056c7-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="056c7-175">`PasswordField`元件是在另一個元件中使用：</span><span class="sxs-lookup"><span data-stu-id="056c7-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="056c7-176">在叫用系結委派的方法中執行檢查或陷阱錯誤。</span><span class="sxs-lookup"><span data-stu-id="056c7-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="056c7-177">下列範例會在密碼值中使用空格時，為使用者提供立即的意見反應：</span><span class="sxs-lookup"><span data-stu-id="056c7-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="056c7-178">如需 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 的詳細資訊，請參閱 <xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="056c7-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="056c7-179">跨兩個以上的元件進行系結</span><span class="sxs-lookup"><span data-stu-id="056c7-179">Bind across more than two components</span></span>

<span data-ttu-id="056c7-180">您可以透過任意數目的嵌套元件進行系結，但您必須遵守單向的資料流程：</span><span class="sxs-lookup"><span data-stu-id="056c7-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="056c7-181">變更通知會在階層中 *往上流動* 。</span><span class="sxs-lookup"><span data-stu-id="056c7-181">Change notifications *flow up the hierarchy* .</span></span>
* <span data-ttu-id="056c7-182">新的參數值會在階層中 *往下流動* 。</span><span class="sxs-lookup"><span data-stu-id="056c7-182">New parameter values *flow down the hierarchy* .</span></span>

<span data-ttu-id="056c7-183">常見和建議的方法是只將基礎資料儲存在父元件中，以避免任何必須更新狀態的混淆。</span><span class="sxs-lookup"><span data-stu-id="056c7-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="056c7-184">下列元件示範上述概念：</span><span class="sxs-lookup"><span data-stu-id="056c7-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="056c7-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="056c7-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="056c7-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="056c7-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="056c7-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="056c7-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private async Task ChangeValue()
    {
        await PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="056c7-188">如需更適合在不需要嵌套的元件之間共用記憶體資料的替代方法，請參閱 <xref:blazor/state-management#in-memory-state-container-service> 。</span><span class="sxs-lookup"><span data-stu-id="056c7-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="056c7-189">其他資源</span><span class="sxs-lookup"><span data-stu-id="056c7-189">Additional resources</span></span>

* [<span data-ttu-id="056c7-190">系結至表單中的選項按鈕</span><span class="sxs-lookup"><span data-stu-id="056c7-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="056c7-191">將 `<select>` 元件選項系結至 `null` 表單中的 c # 物件值</span><span class="sxs-lookup"><span data-stu-id="056c7-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
