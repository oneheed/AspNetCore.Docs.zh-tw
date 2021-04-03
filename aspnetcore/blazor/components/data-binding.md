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
ms.openlocfilehash: 6e2d295289d268ef306cae53a6fbdbaac309cb87
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081542"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="7689b-103">ASP.NET Core Blazor 資料系結</span><span class="sxs-lookup"><span data-stu-id="7689b-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="7689b-104">Razor 元件提供具有 [`@bind`](xref:mvc/views/razor#bind) Razor 欄位、屬性或運算式值之指示詞屬性的資料系結功能 Razor 。</span><span class="sxs-lookup"><span data-stu-id="7689b-104">Razor components provide data binding features with the [`@bind`](xref:mvc/views/razor#bind) Razor directive attribute with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="7689b-105">下列範例會系結：</span><span class="sxs-lookup"><span data-stu-id="7689b-105">The following example binds:</span></span>

* <span data-ttu-id="7689b-106">`<input>`C # 欄位的元素值 `inputValue` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-106">An `<input>` element value to the C# `inputValue` field.</span></span>
* <span data-ttu-id="7689b-107">C # 屬性的第二個 `<input>` 元素值 `InputValue` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-107">A second `<input>` element value to the C# `InputValue` property.</span></span>

<span data-ttu-id="7689b-108">當 `<input>` 元素失去焦點時，就會更新其系結欄位或屬性。</span><span class="sxs-lookup"><span data-stu-id="7689b-108">When an `<input>` element loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="7689b-109">`Pages/Bind.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-109">`Pages/Bind.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

<span data-ttu-id="7689b-110">只有在轉譯元件時，才會更新 UI 中的文字方塊，而不是回應變更欄位或屬性的值。</span><span class="sxs-lookup"><span data-stu-id="7689b-110">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="7689b-111">由於元件會在事件處理常式程式碼執行之後自行轉譯，因此在觸發事件處理常式之後，欄位和屬性更新通常會立即反映在 UI 中。</span><span class="sxs-lookup"><span data-stu-id="7689b-111">Since components render themselves after event handler code executes, field and property updates are usually reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="7689b-112">下列範例示範如何在 HTML 中撰寫資料系結，並將屬性系結 `InputValue` 至第二個 `<input>` 元素的 `value` 和 [`onchange`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange) 屬性。</span><span class="sxs-lookup"><span data-stu-id="7689b-112">As a demonstration of how data binding composes in HTML, the following example binds the `InputValue` property to the second `<input>` element's `value` and [`onchange`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange) attributes.</span></span> <span data-ttu-id="7689b-113">*下列範例中的第二個 `<input>` 元素是概念示範，並非建議您如何在元件中系結資料 Razor 。*</span><span class="sxs-lookup"><span data-stu-id="7689b-113">*The second `<input>` element in the following example is a concept demonstration and isn't meant to suggest how you should bind data in Razor components.*</span></span>

<span data-ttu-id="7689b-114">`Pages/BindTheory.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-114">`Pages/BindTheory.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

<span data-ttu-id="7689b-115">轉譯 `BindTheory` 元件時， `value` HTML 示範元素的會 `<input>` 來自 `InputValue` 屬性。</span><span class="sxs-lookup"><span data-stu-id="7689b-115">When the `BindTheory` component is rendered, the `value` of the HTML demonstration `<input>` element comes from the `InputValue` property.</span></span> <span data-ttu-id="7689b-116">當使用者在文字方塊中輸入值並變更元素焦點時， `onchange` 就會引發事件，並將 `InputValue` 屬性設定為變更的值。</span><span class="sxs-lookup"><span data-stu-id="7689b-116">When the user enters a value in the text box and changes element focus, the `onchange` event is fired and the `InputValue` property is set to the changed value.</span></span> <span data-ttu-id="7689b-117">事實上，程式碼執行較複雜，因為它會 [`@bind`](xref:mvc/views/razor#bind) 處理型別轉換的執行情況。</span><span class="sxs-lookup"><span data-stu-id="7689b-117">In reality, code execution is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="7689b-118">一般來說，會將 [`@bind`](xref:mvc/views/razor#bind) 運算式的目前值與屬性產生關聯 `value` ，並使用已註冊的處理常式來處理變更。</span><span class="sxs-lookup"><span data-stu-id="7689b-118">In general, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="7689b-119">將屬性或欄位系結到其他 [檔物件模型 (dom) ](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件，方法是包含 `@bind:event="{EVENT}"` 包含預留位置之 DOM 事件的屬性 `{EVENT}` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-119">Bind a property or field on other [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events by including an `@bind:event="{EVENT}"` attribute with a DOM event for the `{EVENT}` placeholder.</span></span> <span data-ttu-id="7689b-120">下列範例 `InputValue` `<input>` 會在觸發元素的[ `oninput` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput)時，將屬性系結至專案的值。</span><span class="sxs-lookup"><span data-stu-id="7689b-120">The following example binds the `InputValue` property to the `<input>` element's value when the element's [`oninput` event](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) is triggered.</span></span> <span data-ttu-id="7689b-121">不同于元素失去焦點時所引發的[ `onchange` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange)， [`oninput`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) 當文字方塊的值變更時，就會引發。</span><span class="sxs-lookup"><span data-stu-id="7689b-121">Unlike the [`onchange` event](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange), which fires when the element loses focus, [`oninput`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) fires when the value of the text box changes.</span></span>

<span data-ttu-id="7689b-122">`Page/BindEvent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-122">`Page/BindEvent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

<span data-ttu-id="7689b-123">Razor 屬性系結會區分大小寫：</span><span class="sxs-lookup"><span data-stu-id="7689b-123">Razor attribute binding is case sensitive:</span></span>

* <span data-ttu-id="7689b-124">`@bind` 和 `@bind:event` 都有效。</span><span class="sxs-lookup"><span data-stu-id="7689b-124">`@bind` and `@bind:event` are valid.</span></span>
* <span data-ttu-id="7689b-125">`@Bind`/`@Bind:Event` (大寫字母 `B` 和 `E`) 或 `@BIND` / `@BIND:EVENT` (所有大寫字母) **都無效**。</span><span class="sxs-lookup"><span data-stu-id="7689b-125">`@Bind`/`@Bind:Event` (capital letters `B` and `E`) or `@BIND`/`@BIND:EVENT` (all capital letters) **are invalid**.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="7689b-126">無法剖析的值</span><span class="sxs-lookup"><span data-stu-id="7689b-126">Unparsable values</span></span>

<span data-ttu-id="7689b-127">當使用者提供無法剖析的值給資料系結專案時，會在觸發 bind 事件時，將無法剖析的值自動還原為先前的值。</span><span class="sxs-lookup"><span data-stu-id="7689b-127">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="7689b-128">請考慮下列元件，其中 `<input>` 元素會系結至 `int` 具有初始值的型別 `123` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-128">Consider the following component, where an `<input>` element is bound to an `int` type with an initial value of `123`.</span></span>

<span data-ttu-id="7689b-129">`Pages/UnparsableValues.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-129">`Pages/UnparsableValues.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

<span data-ttu-id="7689b-130">根據預設，系結會套用至元素的 `onchange` 事件。</span><span class="sxs-lookup"><span data-stu-id="7689b-130">By default, binding applies to the element's `onchange` event.</span></span> <span data-ttu-id="7689b-131">如果使用者將文字方塊的專案值更新為 `123.45` 並變更焦點，則在引發時，元素的值會還原為 `123` `onchange` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-131">If the user updates the value of the text box's entry to `123.45` and changes the focus, the element's value is reverted to `123` when `onchange` fires.</span></span> <span data-ttu-id="7689b-132">當值 `123.45` 被拒絕時，若要改用的原始值 `123` ，使用者瞭解其值不會被接受。</span><span class="sxs-lookup"><span data-stu-id="7689b-132">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="7689b-133">針對 `oninput` () 的事件 `@bind:event="oninput"` ，回復值會在導入未剖析值的任何按鍵之後發生。</span><span class="sxs-lookup"><span data-stu-id="7689b-133">For the `oninput` event (`@bind:event="oninput"`), a value reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="7689b-134">以具有系結類型的事件為目標時 `oninput` `int` ，使用者無法輸入點 (`.`) 字元。</span><span class="sxs-lookup"><span data-stu-id="7689b-134">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a dot (`.`) character.</span></span> <span data-ttu-id="7689b-135">`.`立即移除) 字元 (的點，讓使用者收到只允許整數的立即回應。</span><span class="sxs-lookup"><span data-stu-id="7689b-135">A dot (`.`) character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="7689b-136">在某些情況下，還原事件的值 `oninput` 並不理想，例如，當使用者應該清除無法剖析的 `<input>` 值時。</span><span class="sxs-lookup"><span data-stu-id="7689b-136">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="7689b-137">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="7689b-137">Alternatives include:</span></span>

* <span data-ttu-id="7689b-138">請勿使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="7689b-138">Don't use the `oninput` event.</span></span> <span data-ttu-id="7689b-139">使用預設 `onchange` 事件，在此情況下，將不會還原無效值，直到專案失去焦點為止。</span><span class="sxs-lookup"><span data-stu-id="7689b-139">Use the default `onchange` event, where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="7689b-140">系結至可為 null 的型別（例如或）， `int?` `string` 並提供 [自訂 `get` 和 `set` 存取子邏輯](#custom-binding-formats) 來處理不正確專案。</span><span class="sxs-lookup"><span data-stu-id="7689b-140">Bind to a nullable type, such as `int?` or `string` and provide [custom `get` and `set` accessor logic](#custom-binding-formats) to handle invalid entries.</span></span>
* <span data-ttu-id="7689b-141">使用 [表單驗證元件](xref:blazor/forms-validation)，例如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 。</span><span class="sxs-lookup"><span data-stu-id="7689b-141">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="7689b-142">表單驗證元件提供內建支援來管理不正確輸入。</span><span class="sxs-lookup"><span data-stu-id="7689b-142">Form validation components provide built-in support to manage invalid inputs.</span></span> <span data-ttu-id="7689b-143">表單驗證元件：</span><span class="sxs-lookup"><span data-stu-id="7689b-143">Form validation components:</span></span>
  * <span data-ttu-id="7689b-144">允許使用者提供不正確輸入，並在相關聯的上接收驗證錯誤 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="7689b-144">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="7689b-145">在 UI 中顯示驗證錯誤，而不會干擾使用者輸入其他 webform 資料。</span><span class="sxs-lookup"><span data-stu-id="7689b-145">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="7689b-146">格式字串</span><span class="sxs-lookup"><span data-stu-id="7689b-146">Format strings</span></span>

<span data-ttu-id="7689b-147">資料系結 <xref:System.DateTime> 會使用單一格式字串 `@bind:format="{FORMAT STRING}"` ，其中 `{FORMAT STRING}` 預留位置是格式字串。</span><span class="sxs-lookup"><span data-stu-id="7689b-147">Data binding works with a single <xref:System.DateTime> format string using `@bind:format="{FORMAT STRING}"`, where the `{FORMAT STRING}` placeholder is the format string.</span></span> <span data-ttu-id="7689b-148">其他格式運算式（例如貨幣或數位格式）目前無法使用，但可能會在未來的版本中加入。</span><span class="sxs-lookup"><span data-stu-id="7689b-148">Other format expressions, such as currency or number formats, aren't available at this time but might be added in a future release.</span></span>

<span data-ttu-id="7689b-149">`Pages/DateBinding.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-149">`Pages/DateBinding.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

<span data-ttu-id="7689b-150">在上述程式碼中， `<input>` 元素的欄位類型 (`type` 屬性) 預設為 `text` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-150">In the preceding code, the `<input>` element's field type (`type` attribute) defaults to `text`.</span></span>

<span data-ttu-id="7689b-151">可為 null <xref:System.DateTime?displayProperty=fullName> 且 <xref:System.DateTimeOffset?displayProperty=fullName> 受支援：</span><span class="sxs-lookup"><span data-stu-id="7689b-151">Nullable <xref:System.DateTime?displayProperty=fullName> and <xref:System.DateTimeOffset?displayProperty=fullName> are supported:</span></span>

```csharp
private DateTime? date;
private DateTimeOffset? dateOffset;
```

<span data-ttu-id="7689b-152">`date`由於 Blazor 具有格式化日期的內建支援，因此不建議指定欄位類型的格式。</span><span class="sxs-lookup"><span data-stu-id="7689b-152">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="7689b-153">在建議的情況下， `yyyy-MM-dd` 如果格式是以欄位類型提供，只使用日期格式讓系結正常運作 `date` ：</span><span class="sxs-lookup"><span data-stu-id="7689b-153">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="custom-binding-formats"></a><span data-ttu-id="7689b-154">自訂系結格式</span><span class="sxs-lookup"><span data-stu-id="7689b-154">Custom binding formats</span></span>

<span data-ttu-id="7689b-155">[C # `get` 和 `set` 存取](/dotnet/csharp/programming-guide/classes-and-structs/using-properties) 子可以用來建立自訂系結格式行為，如下列元件所示 `DecimalBinding` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-155">[C# `get` and `set` accessors](/dotnet/csharp/programming-guide/classes-and-structs/using-properties) can be used to create custom binding format behavior, as the following `DecimalBinding` component demonstrates.</span></span> <span data-ttu-id="7689b-156">元件會透過 `<input>` `string` 屬性 () ，將一個正或負的十進位與最多三個小數位數系結至元素 `DecimalValue` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-156">The component binds a positive or negative decimal with up to three decimal places to an `<input>` element by way of a `string` property (`DecimalValue`).</span></span>

<span data-ttu-id="7689b-157">`Pages/DecimalBinding.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-157">`Pages/DecimalBinding.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

## <a name="binding-with-component-parameters"></a><span data-ttu-id="7689b-158">使用元件參數進行系結</span><span class="sxs-lookup"><span data-stu-id="7689b-158">Binding with component parameters</span></span>

<span data-ttu-id="7689b-159">常見的案例是將子元件的屬性系結至其父元件中的屬性。</span><span class="sxs-lookup"><span data-stu-id="7689b-159">A common scenario is binding a property of a child component to a property in its parent component.</span></span> <span data-ttu-id="7689b-160">此案例稱為 *連鎖* 系結，因為有多個層級的系結同時發生。</span><span class="sxs-lookup"><span data-stu-id="7689b-160">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="7689b-161">[元件參數](xref:blazor/components/index#component-parameters) 允許父元件的系結屬性與 `@bind-{PROPERTY}` 語法，其中 `{PROPERTY}` 預留位置是要系結的屬性。</span><span class="sxs-lookup"><span data-stu-id="7689b-161">[Component parameters](xref:blazor/components/index#component-parameters) permit binding properties of a parent component with `@bind-{PROPERTY}` syntax, where the `{PROPERTY}` placeholder is the property to bind.</span></span>

<span data-ttu-id="7689b-162">您無法使用子元件中的語法來執行連結系結 [`@bind`](xref:mvc/views/razor#bind) 。</span><span class="sxs-lookup"><span data-stu-id="7689b-162">You can't implement chained binds with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="7689b-163">必須個別指定事件處理常式和值，以支援從子元件的父系更新屬性。</span><span class="sxs-lookup"><span data-stu-id="7689b-163">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="7689b-164">父元件仍會利用 [`@bind`](xref:mvc/views/razor#bind) 語法來設定與子元件的資料系結。</span><span class="sxs-lookup"><span data-stu-id="7689b-164">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the databinding with the child component.</span></span>

<span data-ttu-id="7689b-165">下列 `ChildBind` 元件具有 `Year` 元件參數和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 。</span><span class="sxs-lookup"><span data-stu-id="7689b-165">The following `ChildBind` component has a `Year` component parameter and an <xref:Microsoft.AspNetCore.Components.EventCallback%601>.</span></span> <span data-ttu-id="7689b-166">依照慣例， <xref:Microsoft.AspNetCore.Components.EventCallback%601> 參數的必須以 "" 尾碼命名為元件參數名稱 `Changed` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-166">By convention, the <xref:Microsoft.AspNetCore.Components.EventCallback%601> for the parameter must be named as the component parameter name with a "`Changed`" suffix.</span></span> <span data-ttu-id="7689b-167">命名語法是 `{PARAMETER NAME}Changed` ，其中 `{PARAMETER NAME}` 預留位置是參數名稱。</span><span class="sxs-lookup"><span data-stu-id="7689b-167">The naming syntax is `{PARAMETER NAME}Changed`, where the `{PARAMETER NAME}` placeholder is the parameter name.</span></span> <span data-ttu-id="7689b-168">在下列範例中， <xref:Microsoft.AspNetCore.Components.EventCallback%601> 名為 `YearChanged` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-168">In the following example, the <xref:Microsoft.AspNetCore.Components.EventCallback%601> is named `YearChanged`.</span></span>

<span data-ttu-id="7689b-169"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 使用提供的引數叫用與系結相關聯的委派，並分派已變更屬性的事件通知。</span><span class="sxs-lookup"><span data-stu-id="7689b-169"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="7689b-170">`Shared/ChildBind.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-170">`Shared/ChildBind.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

<span data-ttu-id="7689b-171">如需事件和的詳細資訊 <xref:Microsoft.AspNetCore.Components.EventCallback%601> ，請參閱本文的 *>app >eventcallback* 一節 <xref:blazor/components/event-handling#eventcallback> 。</span><span class="sxs-lookup"><span data-stu-id="7689b-171">For more information on events and <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see the *EventCallback* section of the <xref:blazor/components/event-handling#eventcallback> article.</span></span>

<span data-ttu-id="7689b-172">在下列 `Parent` 元件中， `year` 欄位會系結至 `Year` 子元件的參數。</span><span class="sxs-lookup"><span data-stu-id="7689b-172">In the following `Parent` component, the `year` field is bound to the `Year` parameter of the child component.</span></span> <span data-ttu-id="7689b-173">`Year`參數是可系結的，因為它具有 `YearChanged` 符合參數類型的伴隨事件 `Year` 。</span><span class="sxs-lookup"><span data-stu-id="7689b-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="7689b-174">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-174">`Pages/Parent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

<span data-ttu-id="7689b-175">依照慣例，屬性可以系結至對應的事件處理常式，其方式 `@bind-{PROPERTY}:event` 是將指派給處理常式的屬性（attribute），其中 `{PROPERTY}` 預留位置是屬性（property）。</span><span class="sxs-lookup"><span data-stu-id="7689b-175">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler, where the `{PROPERTY}` placeholder is the property.</span></span> <span data-ttu-id="7689b-176">`<ChildBind @bind-Year="year" />` 相當於撰寫：</span><span class="sxs-lookup"><span data-stu-id="7689b-176">`<ChildBind @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<ChildBind @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="7689b-177">在更複雜的真實世界範例中，下列 `Password` 元件：</span><span class="sxs-lookup"><span data-stu-id="7689b-177">In a more sophisticated and real-world example, the following `Password` component:</span></span>

* <span data-ttu-id="7689b-178">將 `<input>` 元素的值設定為 `password` 欄位。</span><span class="sxs-lookup"><span data-stu-id="7689b-178">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="7689b-179">將屬性的變更公開 `Password` 至父代元件 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) ，該元件會傳入子系欄位的目前值 `password` 做為其引數。</span><span class="sxs-lookup"><span data-stu-id="7689b-179">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="7689b-180">使用 `onclick` 事件來觸發 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="7689b-180">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="7689b-181">如需詳細資訊，請參閱<xref:blazor/components/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="7689b-181">For more information, see <xref:blazor/components/event-handling>.</span></span>

<span data-ttu-id="7689b-182">`Shared/PasswordEntry.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-182">`Shared/PasswordEntry.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

<span data-ttu-id="7689b-183">`PasswordEntry`元件是在另一個元件中使用，例如下列 `PasswordBinding` 元件範例。</span><span class="sxs-lookup"><span data-stu-id="7689b-183">The `PasswordEntry` component is used in another component, such as the following `PasswordBinding` component example.</span></span>

<span data-ttu-id="7689b-184">`Pages/PasswordBinding.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-184">`Pages/PasswordBinding.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

<span data-ttu-id="7689b-185">在叫用系結委派的方法中執行檢查或陷阱錯誤。</span><span class="sxs-lookup"><span data-stu-id="7689b-185">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="7689b-186">下列修訂的 `PasswordEntry` 元件會在密碼的值中使用空格時，為使用者提供立即的意見反應。</span><span class="sxs-lookup"><span data-stu-id="7689b-186">The following revised `PasswordEntry` component provides immediate feedback to the user if a space is used in the password's value.</span></span>

<span data-ttu-id="7689b-187">`Shared/PasswordEntry.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-187">`Shared/PasswordEntry.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="7689b-188">跨兩個以上的元件進行系結</span><span class="sxs-lookup"><span data-stu-id="7689b-188">Bind across more than two components</span></span>

<span data-ttu-id="7689b-189">您可以透過任意數目的嵌套元件來系結參數，但您必須遵循單向資料流程：</span><span class="sxs-lookup"><span data-stu-id="7689b-189">You can bind parameters through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="7689b-190">變更通知會在階層中 *往上流動*。</span><span class="sxs-lookup"><span data-stu-id="7689b-190">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="7689b-191">新的參數值會在階層中 *往下流動*。</span><span class="sxs-lookup"><span data-stu-id="7689b-191">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="7689b-192">常見和建議的方法是只將基礎資料儲存在父元件中，以避免任何必須更新狀態的混淆，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="7689b-192">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated, as shown in the following example.</span></span>

<span data-ttu-id="7689b-193">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-193">`Pages/Parent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

<span data-ttu-id="7689b-194">`Shared/NestedChild.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-194">`Shared/NestedChild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

<span data-ttu-id="7689b-195">`Shared/NestedGrandchild.razor`:</span><span class="sxs-lookup"><span data-stu-id="7689b-195">`Shared/NestedGrandchild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

<span data-ttu-id="7689b-196">如需適合用來在記憶體中共用資料的替代方法，以及跨不一定要進行嵌套的元件，請參閱 <xref:blazor/state-management> 。</span><span class="sxs-lookup"><span data-stu-id="7689b-196">For an alternative approach suited to sharing data in memory and across components that aren't necessarily nested, see <xref:blazor/state-management>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7689b-197">其他資源</span><span class="sxs-lookup"><span data-stu-id="7689b-197">Additional resources</span></span>

* <xref:blazor/forms-validation>
* [<span data-ttu-id="7689b-198">系結至表單中的選項按鈕</span><span class="sxs-lookup"><span data-stu-id="7689b-198">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="7689b-199">將 `<select>` 元件選項系結至 `null` 表單中的 c # 物件值</span><span class="sxs-lookup"><span data-stu-id="7689b-199">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
* [<span data-ttu-id="7689b-200">ASP.NET Core Blazor 事件處理： `EventCallback` 區段</span><span class="sxs-lookup"><span data-stu-id="7689b-200">ASP.NET Core Blazor event handling: `EventCallback` section</span></span>](xref:blazor/components/event-handling#eventcallback)
