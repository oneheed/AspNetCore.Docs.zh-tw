---
title: 'ASP.NET Core :::no-loc(Blazor)::: 表單和驗證'
author: guardrex
description: '瞭解如何在中使用表單和欄位驗證案例 :::no-loc(Blazor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
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
uid: blazor/forms-validation
ms.openlocfilehash: ad244c29c8e8e904793745119366cd677389b12d
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690605"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="4be25-103">ASP.NET Core :::no-loc(Blazor)::: 表單和驗證</span><span class="sxs-lookup"><span data-stu-id="4be25-103">ASP.NET Core :::no-loc(Blazor)::: forms and validation</span></span>

<span data-ttu-id="4be25-104">[Daniel Roth](https://github.com/danroth27)、 [Rémi Bourgarel](https://remibou.github.io/)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4be25-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4be25-105">:::no-loc(Blazor):::使用[資料批註](xref:mvc/models/validation)可支援表單和驗證。</span><span class="sxs-lookup"><span data-stu-id="4be25-105">Forms and validation are supported in :::no-loc(Blazor)::: using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="4be25-106">下列 `ExampleModel` 類型會使用資料批註來定義驗證邏輯：</span><span class="sxs-lookup"><span data-stu-id="4be25-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="4be25-107">表單是使用元件所定義的 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="4be25-108">下列表單會示範一般元素、元件和程式 :::no-loc(Razor)::: 代碼：</span><span class="sxs-lookup"><span data-stu-id="4be25-108">The following form demonstrates typical elements, components, and :::no-loc(Razor)::: code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="4be25-109">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="4be25-109">In the preceding example:</span></span>

* <span data-ttu-id="4be25-110">表單會 `name` 使用型別中定義的驗證來驗證欄位中的使用者輸入 `ExampleModel` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="4be25-111">此模型會在元件的區塊中建立 `@code` ，並保存在私用欄位中 (`exampleModel`) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="4be25-112">此欄位會指派給專案的 `Model` 屬性 `<EditForm>` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="4be25-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText>元件的系結 `@bind-Value` ：</span><span class="sxs-lookup"><span data-stu-id="4be25-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="4be25-114">模型屬性 (`exampleModel.Name`) 至 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 元件的 `Value` 屬性。</span><span class="sxs-lookup"><span data-stu-id="4be25-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="4be25-115">如需屬性系結的詳細資訊，請參閱 <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="4be25-116">元件屬性的變更事件委派 <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="4be25-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>[驗證程式元件](#validator-components)會附加使用資料批註的驗證支援。</span><span class="sxs-lookup"><span data-stu-id="4be25-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="4be25-118">此 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 元件會摘要說明驗證訊息。</span><span class="sxs-lookup"><span data-stu-id="4be25-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="4be25-119">`HandleValidSubmit` 當表單成功提交 (通過驗證) 時，就會觸發。</span><span class="sxs-lookup"><span data-stu-id="4be25-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="4be25-120">內建表單元件</span><span class="sxs-lookup"><span data-stu-id="4be25-120">Built-in forms components</span></span>

<span data-ttu-id="4be25-121">有一組內建元件可用來接收及驗證使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="4be25-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="4be25-122">當輸入變更和提交表單時，會驗證輸入。</span><span class="sxs-lookup"><span data-stu-id="4be25-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="4be25-123">下表顯示可用的輸入元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="4be25-124">輸入元件</span><span class="sxs-lookup"><span data-stu-id="4be25-124">Input component</span></span> | <span data-ttu-id="4be25-125">呈現為&hellip;</span><span class="sxs-lookup"><span data-stu-id="4be25-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="4be25-126">輸入元件</span><span class="sxs-lookup"><span data-stu-id="4be25-126">Input component</span></span> | <span data-ttu-id="4be25-127">呈現為&hellip;</span><span class="sxs-lookup"><span data-stu-id="4be25-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="4be25-128">`InputRadio`和 `InputRadioGroup` 元件可在 ASP.NET Core 5.0 或更新版本中使用。</span><span class="sxs-lookup"><span data-stu-id="4be25-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="4be25-129">如需詳細資訊，請選取此文章的5.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="4be25-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="4be25-130">所有輸入元件（包括）都 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 支援任意屬性。</span><span class="sxs-lookup"><span data-stu-id="4be25-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="4be25-131">任何不符合元件參數的屬性都會加入至轉譯的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="4be25-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="4be25-132">輸入元件提供在欄位變更時驗證的預設行為，包括更新欄位 CSS 類別以反映欄位狀態。</span><span class="sxs-lookup"><span data-stu-id="4be25-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="4be25-133">某些元件包含有用的剖析邏輯。</span><span class="sxs-lookup"><span data-stu-id="4be25-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="4be25-134">例如，藉 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 由將無法 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 分析的值註冊為驗證錯誤，以正常方式處理無法剖析的值。</span><span class="sxs-lookup"><span data-stu-id="4be25-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="4be25-135">可以接受 null 值的類型也支援目標欄位的可 null 性 (例如， `int?`) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="4be25-136">下列 `Starship` 類型使用較早的屬性和資料批註集來定義驗證邏輯 `ExampleModel` ：</span><span class="sxs-lookup"><span data-stu-id="4be25-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="4be25-137">在上述範例中， `Description` 是選擇性的，因為沒有任何資料批註存在。</span><span class="sxs-lookup"><span data-stu-id="4be25-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="4be25-138">下列表單會使用模型中所定義的驗證來驗證使用者輸入 `Starship` ：</span><span class="sxs-lookup"><span data-stu-id="4be25-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="4be25-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 會建立做為階層式[值](xref:blazor/components/cascading-values-and-parameters)，以追蹤編輯程式的相關中繼資料，包括已修改的欄位和目前的驗證訊息。</span><span class="sxs-lookup"><span data-stu-id="4be25-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="4be25-140">將 **either** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **或** 指派給 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="4be25-141">不支援同時指派，且會產生 **執行階段錯誤** 。</span><span class="sxs-lookup"><span data-stu-id="4be25-141">Assignment of both isn't supported and generates a **runtime error** .</span></span>

<span data-ttu-id="4be25-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>提供方便的事件，以進行有效和不正確表單提交：</span><span class="sxs-lookup"><span data-stu-id="4be25-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="4be25-143">使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 可使用自訂程式碼來觸發驗證和檢查域值。</span><span class="sxs-lookup"><span data-stu-id="4be25-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="4be25-144">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="4be25-144">In the following example:</span></span>

* <span data-ttu-id="4be25-145">`HandleSubmit`當選取按鈕時，就會執行此方法 **`Submit`** 。</span><span class="sxs-lookup"><span data-stu-id="4be25-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="4be25-146">藉由呼叫來驗證表單 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="4be25-147">會根據驗證結果來執行其他程式碼。</span><span class="sxs-lookup"><span data-stu-id="4be25-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="4be25-148">將商務邏輯放在指派給的方法中 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="4be25-149">Framework API 不存在，無法從直接清除驗證訊息 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="4be25-150">因此，我們通常不建議您將驗證訊息新增至 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 表單中的新。</span><span class="sxs-lookup"><span data-stu-id="4be25-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="4be25-151">若要管理驗證訊息，請使用 [驗證程式元件](#validator-components) 搭配您的 [商務邏輯驗證程式代碼](#business-logic-validation)，如本文中所述。</span><span class="sxs-lookup"><span data-stu-id="4be25-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="4be25-152">顯示名稱支援</span><span class="sxs-lookup"><span data-stu-id="4be25-152">Display name support</span></span>

<span data-ttu-id="4be25-153">*本節適用于 .NET 5 候選版 1 (RC1) 或更新版本中的 ASP.NET Core。*</span><span class="sxs-lookup"><span data-stu-id="4be25-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="4be25-154">下列內建元件支援使用參數顯示名稱 `DisplayName` ：</span><span class="sxs-lookup"><span data-stu-id="4be25-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="4be25-155">在下列 `InputDate` 元件範例中：</span><span class="sxs-lookup"><span data-stu-id="4be25-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="4be25-156"> () 的顯示名稱 `DisplayName` 設為 `birthday` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="4be25-157">元件會系結至 `BirthDate` 屬性作為型別 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="4be25-158">如果使用者未提供日期值，驗證錯誤會顯示為：</span><span class="sxs-lookup"><span data-stu-id="4be25-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="4be25-159">驗證程式元件</span><span class="sxs-lookup"><span data-stu-id="4be25-159">Validator components</span></span>

<span data-ttu-id="4be25-160">驗證程式元件會藉由管理表單的來支援表單驗證 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="4be25-161">:::no-loc(Blazor):::架構會提供 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件，以根據驗證屬性將驗證支援附加至表單[ (資料批註) ](xref:mvc/models/validation#validation-attributes)。</span><span class="sxs-lookup"><span data-stu-id="4be25-161">The :::no-loc(Blazor)::: framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="4be25-162">建立自訂驗證程式元件，在相同頁面上處理不同表單的驗證訊息，或在表單處理的不同步驟中處理相同表單上的驗證訊息，例如用戶端驗證，接著伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="4be25-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="4be25-163">本節所示的驗證程式元件範例 `CustomValidator` 將用於本文的下列各節：</span><span class="sxs-lookup"><span data-stu-id="4be25-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="4be25-164">商務邏輯驗證</span><span class="sxs-lookup"><span data-stu-id="4be25-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="4be25-165">伺服器驗證</span><span class="sxs-lookup"><span data-stu-id="4be25-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="4be25-166">在許多情況下，您可以使用自訂資料批註驗證屬性來取代自訂驗證程式元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="4be25-167">套用至表單模型的自訂屬性會使用元件來啟用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="4be25-168">與伺服器端驗證搭配使用時，套用至模型的任何自訂屬性都必須是伺服器上的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="4be25-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="4be25-169">如需詳細資訊，請參閱<xref:mvc/models/validation#alternatives-to-built-in-attributes>。</span><span class="sxs-lookup"><span data-stu-id="4be25-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="4be25-170">從下列程式建立驗證程式元件 <xref:Microsoft.AspNetCore.Components.ComponentBase> ：</span><span class="sxs-lookup"><span data-stu-id="4be25-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="4be25-171">表單的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 是元件的串聯 [參數](xref:blazor/components/cascading-values-and-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="4be25-172">當驗證程式元件初始化時， <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 會建立新的來維護目前的表單錯誤清單。</span><span class="sxs-lookup"><span data-stu-id="4be25-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="4be25-173">當表單元件中的開發人員程式碼呼叫方法時，訊息存放區會接收錯誤 `DisplayErrors` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="4be25-174">錯誤會傳遞至中的 `DisplayErrors` 方法 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="4be25-175">在字典中，索引鍵是有一或多個錯誤的表單欄位名稱。</span><span class="sxs-lookup"><span data-stu-id="4be25-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="4be25-176">值是錯誤清單。</span><span class="sxs-lookup"><span data-stu-id="4be25-176">The value is the error list.</span></span>
* <span data-ttu-id="4be25-177">當發生下列任何一種情況時，就會清除訊息：</span><span class="sxs-lookup"><span data-stu-id="4be25-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="4be25-178"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>當引發事件時，會在上要求驗證 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="4be25-179">所有錯誤都會清除。</span><span class="sxs-lookup"><span data-stu-id="4be25-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="4be25-180">當引發事件時，表單中的欄位 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 就會變更。</span><span class="sxs-lookup"><span data-stu-id="4be25-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="4be25-181">只會清除欄位的錯誤。</span><span class="sxs-lookup"><span data-stu-id="4be25-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="4be25-182">`ClearErrors`方法是由開發人員程式碼呼叫。</span><span class="sxs-lookup"><span data-stu-id="4be25-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="4be25-183">所有錯誤都會清除。</span><span class="sxs-lookup"><span data-stu-id="4be25-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace :::no-loc(Blazor):::Sample.Client
{
    public class CustomValidator : ComponentBase
    {
        private ValidationMessageStore messageStore;

        [CascadingParameter]
        private EditContext CurrentEditContext { get; set; }

        protected override void OnInitialized()
        {
            if (CurrentEditContext == null)
            {
                throw new InvalidOperationException(
                    $"{nameof(CustomValidator)} requires a cascading " +
                    $"parameter of type {nameof(EditContext)}. " +
                    $"For example, you can use {nameof(CustomValidator)} " +
                    $"inside an {nameof(EditForm)}.");
            }

            messageStore = new ValidationMessageStore(CurrentEditContext);

            CurrentEditContext.OnValidationRequested += (s, e) => 
                messageStore.Clear();
            CurrentEditContext.OnFieldChanged += (s, e) => 
                messageStore.Clear(e.FieldIdentifier);
        }

        public void DisplayErrors(Dictionary<string, List<string>> errors)
        {
            foreach (var err in errors)
            {
                messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
            }

            CurrentEditContext.NotifyValidationStateChanged();
        }

        public void ClearErrors()
        {
            messageStore.Clear();
            CurrentEditContext.NotifyValidationStateChanged();
        }
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="4be25-184">商務邏輯驗證</span><span class="sxs-lookup"><span data-stu-id="4be25-184">Business logic validation</span></span>

<span data-ttu-id="4be25-185">您可以使用接收字典中的表單錯誤的 [驗證程式元件](#validator-components) 來完成商務邏輯驗證。</span><span class="sxs-lookup"><span data-stu-id="4be25-185">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="4be25-186">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="4be25-186">In the following example:</span></span>

* <span data-ttu-id="4be25-187">本文的「 `CustomValidator` [驗證程式元件](#validator-components) 」一節中會使用此元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-187">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="4be25-188">`Description`如果使用者選取 [出 `Defense` 貨分類] () ，則驗證會要求出貨的描述 () 的值 `Classification` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-188">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="4be25-189">在元件中設定驗證訊息時，會將它們新增至驗證程式， <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 並顯示在 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> ：</span><span class="sxs-lookup"><span data-stu-id="4be25-189">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="4be25-190">除了使用 [驗證元件](#validator-components)之外，也可以使用資料批註驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="4be25-190">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="4be25-191">套用至表單模型的自訂屬性會使用元件來啟用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-191">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="4be25-192">與伺服器端驗證搭配使用時，屬性必須是伺服器上的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="4be25-192">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="4be25-193">如需詳細資訊，請參閱<xref:mvc/models/validation#alternatives-to-built-in-attributes>。</span><span class="sxs-lookup"><span data-stu-id="4be25-193">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="4be25-194">伺服器驗證</span><span class="sxs-lookup"><span data-stu-id="4be25-194">Server validation</span></span>

<span data-ttu-id="4be25-195">您可以使用伺服器驗證程式 [元件](#validator-components)來完成伺服器驗證：</span><span class="sxs-lookup"><span data-stu-id="4be25-195">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="4be25-196">使用元件，處理表單中的用戶端驗證 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-196">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="4be25-197">當表單通過用戶端驗證時 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> ， (稱為) ，請將傳送 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> 至後端伺服器 API 以進行表單處理。</span><span class="sxs-lookup"><span data-stu-id="4be25-197">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="4be25-198">伺服器上的進程模型驗證。</span><span class="sxs-lookup"><span data-stu-id="4be25-198">Process model validation on the server.</span></span>
* <span data-ttu-id="4be25-199">伺服器 API 包含內建的架構資料批註驗證，以及開發人員所提供的自訂驗證邏輯。</span><span class="sxs-lookup"><span data-stu-id="4be25-199">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="4be25-200">如果驗證是在伺服器上通過，請處理表單並將成功狀態碼傳回 ( *200-確定* ) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-200">If validation passes on the server, process the form and send back a success status code ( *200 - OK* ).</span></span> <span data-ttu-id="4be25-201">如果驗證失敗，會傳回失敗狀態碼 ( *400-錯誤的要求* ) 和欄位驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="4be25-201">If validation fails, return a failure status code ( *400 - Bad Request* ) and the field validation errors.</span></span>
* <span data-ttu-id="4be25-202">請停用成功的表單，或顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="4be25-202">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="4be25-203">下列範例是根據：</span><span class="sxs-lookup"><span data-stu-id="4be25-203">The following example is based on:</span></span>

* <span data-ttu-id="4be25-204">主控 :::no-loc(Blazor)::: 的[ :::no-loc(Blazor)::: 專案範本](xref:blazor/hosting-models#blazor-webassembly)所建立的裝載方案。</span><span class="sxs-lookup"><span data-stu-id="4be25-204">A hosted :::no-loc(Blazor)::: solution created by the [:::no-loc(Blazor)::: Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="4be25-205">此範例可以與 :::no-loc(Blazor)::: [安全性和 :::no-loc(Identity)::: 檔](xref:blazor/security/webassembly/index#implementation-guidance)中所述的任何安全託管解決方案搭配使用。</span><span class="sxs-lookup"><span data-stu-id="4be25-205">The example can be used with any of the secure hosted :::no-loc(Blazor)::: solutions described in the [Security and :::no-loc(Identity)::: documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="4be25-206">上述內 [建表單元件](#built-in-forms-components)區段中的 *Starfleet Starship 資料庫* 表單範例。</span><span class="sxs-lookup"><span data-stu-id="4be25-206">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="4be25-207">:::no-loc(Blazor):::架構的 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-207">The :::no-loc(Blazor)::: framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="4be25-208">在 `CustomValidator` [ [驗證程式元件](#validator-components) ] 區段中顯示的元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-208">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="4be25-209">在下列範例中，伺服器 API 會驗證是否針對出貨的描述提供值 (`Description`) 如果使用者選取 [出 `Defense` 貨分類] (`Classification`) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-209">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="4be25-210">將 `Starship` 模型放入方案的專案中， `Shared` 讓用戶端和伺服器應用程式都可以使用此模型。</span><span class="sxs-lookup"><span data-stu-id="4be25-210">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="4be25-211">由於模型需要資料批註，因此請將的封裝參考新增 [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) 至 `Shared` 專案的專案檔：</span><span class="sxs-lookup"><span data-stu-id="4be25-211">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="4be25-212">若要判斷封裝的最新非預覽版本，請參閱套件 **版本歷程記錄** ，網址為 [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)。</span><span class="sxs-lookup"><span data-stu-id="4be25-212">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="4be25-213">在伺服器 API 專案中，加入控制器來處理 starship 驗證要求 (`Controllers/StarshipValidation.cs`) 並傳回失敗的驗證訊息：</span><span class="sxs-lookup"><span data-stu-id="4be25-213">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using :::no-loc(Blazor):::Sample.Shared;

namespace :::no-loc(Blazor):::Sample.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="4be25-214">當伺服器上發生模型系結驗證錯誤時， [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) 通常會傳回 [預設錯誤的要求回應](xref:web-api/index#default-badrequest-response) <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-214">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="4be25-215">回應所包含的資料超過驗證錯誤，如下列範例所示，當 *Starfleet Starship 資料庫* 表單的所有欄位都未送出，而且表單驗證失敗時：</span><span class="sxs-lookup"><span data-stu-id="4be25-215">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="4be25-216">如果伺服器 API 傳回先前的預設 JSON 回應，用戶端可能會剖析回應以取得節點的子系 `errors` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-216">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="4be25-217">不過，剖析檔案並不方便。</span><span class="sxs-lookup"><span data-stu-id="4be25-217">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="4be25-218">在呼叫之後剖析 JSON 需要額外的程式碼 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> ，才能產生 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) 表單驗證錯誤處理的錯誤。</span><span class="sxs-lookup"><span data-stu-id="4be25-218">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="4be25-219">在理想的情況下，伺服器 API 應該只傳回驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="4be25-219">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="4be25-220">若要修改伺服器 API 的回應，使其只傳回驗證錯誤，請變更在中標注的動作所叫用的委派 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-220">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4be25-221">針對 () 的 API 端點 `/StarshipValidation` ，請 <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> 使用傳回 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-221">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="4be25-222">針對任何其他 API 端點，請以新的傳回物件結果來保留預設行為 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> ：</span><span class="sxs-lookup"><span data-stu-id="4be25-222">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="4be25-223">如需詳細資訊，請參閱<xref:web-api/handle-errors#validation-failure-error-response>。</span><span class="sxs-lookup"><span data-stu-id="4be25-223">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="4be25-224">在用戶端專案中，加入 [ [驗證程式元件](#validator-components) ] 區段中顯示的驗證程式元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-224">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="4be25-225">在用戶端專案中，會更新 *Starfleet Starship 資料庫* 表單，以顯示伺服器驗證錯誤並提供 `CustomValidator` 元件的協助。</span><span class="sxs-lookup"><span data-stu-id="4be25-225">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="4be25-226">當伺服器 API 傳回驗證訊息時，就會將它們新增至 `CustomValidator` 元件的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-226">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="4be25-227">表單的可顯示錯誤， <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 表單的格式 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 如下：</span><span class="sxs-lookup"><span data-stu-id="4be25-227">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using :::no-loc(Blazor):::Sample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="4be25-228">您可以使用資料批註驗證屬性作為 [驗證元件](#validator-components)的替代方案。</span><span class="sxs-lookup"><span data-stu-id="4be25-228">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="4be25-229">套用至表單模型的自訂屬性會使用元件來啟用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-229">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="4be25-230">與伺服器端驗證搭配使用時，屬性必須是伺服器上的可執行檔。</span><span class="sxs-lookup"><span data-stu-id="4be25-230">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="4be25-231">如需詳細資訊，請參閱<xref:mvc/models/validation#alternatives-to-built-in-attributes>。</span><span class="sxs-lookup"><span data-stu-id="4be25-231">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="4be25-232">本節中的伺服器端驗證方法適用于此檔集中的任何裝載 :::no-loc(Blazor WebAssembly)::: 解決方案範例：</span><span class="sxs-lookup"><span data-stu-id="4be25-232">The server-side validation approach in this section is suitable for any of the :::no-loc(Blazor WebAssembly)::: hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="4be25-233">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="4be25-233">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="4be25-234">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="4be25-234">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="4be25-235">:::no-loc(Identity)::: 伺服器</span><span class="sxs-lookup"><span data-stu-id="4be25-235">:::no-loc(Identity)::: Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="4be25-236">以輸入事件為基礎的 InputText</span><span class="sxs-lookup"><span data-stu-id="4be25-236">InputText based on the input event</span></span>

<span data-ttu-id="4be25-237">使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 元件建立使用事件的自訂群組件， `input` 而不是 `change` 事件。</span><span class="sxs-lookup"><span data-stu-id="4be25-237">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="4be25-238">在下列範例中， `CustomInputText` 元件會繼承架構的 `InputText` 元件，並將事件系結 (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) 設定至 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="4be25-238">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="4be25-239">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="4be25-239">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="4be25-240">`CustomInputText`元件可以使用於任何位置 <xref:Microsoft.AspNetCore.Components.Forms.InputText> ：</span><span class="sxs-lookup"><span data-stu-id="4be25-240">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="4be25-241">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="4be25-241">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="4be25-242">選項按鈕</span><span class="sxs-lookup"><span data-stu-id="4be25-242">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4be25-243">使用元件搭配 `InputRadio` `InputRadioGroup` 元件來建立選項按鈕群組。</span><span class="sxs-lookup"><span data-stu-id="4be25-243">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="4be25-244">在下列範例中，屬性會加入至 `Starship` 內 [建表單元件](#built-in-forms-components) 區段中所述的模型：</span><span class="sxs-lookup"><span data-stu-id="4be25-244">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="4be25-245">將下列 `enums` 程式新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="4be25-245">Add the following `enums` to the app.</span></span> <span data-ttu-id="4be25-246">建立新的檔案以保存， `enums` 或將新增 `enums` 至檔案 `Starship.cs` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-246">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="4be25-247">讓 `enums` `Starship` 模型和 *Starfleet Starship 資料庫* 形式可存取：</span><span class="sxs-lookup"><span data-stu-id="4be25-247">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="4be25-248">更新內 [建表單元件](#built-in-forms-components)一節中所述的 *Starfleet Starship 資料庫* 表單。</span><span class="sxs-lookup"><span data-stu-id="4be25-248">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="4be25-249">新增要產生的元件：</span><span class="sxs-lookup"><span data-stu-id="4be25-249">Add the components to produce:</span></span>

* <span data-ttu-id="4be25-250">出貨製造商的選項按鈕群組。</span><span class="sxs-lookup"><span data-stu-id="4be25-250">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="4be25-251">出貨色彩和引擎的嵌套選項按鈕群組。</span><span class="sxs-lookup"><span data-stu-id="4be25-251">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="4be25-252">如果 `Name` 省略，則 `InputRadio` 會依最新的上階來群組元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-252">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4be25-253">當您在表單中使用選項按鈕時，資料系結的處理方式會與其他元素不同，因為選項按鈕會評估為群組。</span><span class="sxs-lookup"><span data-stu-id="4be25-253">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="4be25-254">每個選項按鈕的值都是固定的，但選項按鈕群組的值是選取之選項按鈕的值。</span><span class="sxs-lookup"><span data-stu-id="4be25-254">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="4be25-255">下列範例示範如何執行：</span><span class="sxs-lookup"><span data-stu-id="4be25-255">The following example shows how to:</span></span>

* <span data-ttu-id="4be25-256">處理選項按鈕群組的資料系結。</span><span class="sxs-lookup"><span data-stu-id="4be25-256">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="4be25-257">使用自訂群組件的支援驗證 `InputRadio` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-257">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="4be25-258">以下會 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 使用上述 `InputRadio` 元件來取得並驗證使用者的評等：</span><span class="sxs-lookup"><span data-stu-id="4be25-258">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="4be25-259">`<select>`C # 物件值的繫結項目選項 `null`</span><span class="sxs-lookup"><span data-stu-id="4be25-259">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="4be25-260">`<select>`因為下列原因，所以無法以 c # 物件值表示元素選項值 `null` ：</span><span class="sxs-lookup"><span data-stu-id="4be25-260">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="4be25-261">HTML 屬性不能有 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="4be25-261">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="4be25-262">HTML 中最接近的對等專案 `null` ，不是元素中的 html `value` 屬性 `<option>` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-262">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="4be25-263">當您選取 `<option>` 不含 `value` 屬性的時，瀏覽器會將此值視為該元素的 *文字內容* `<option>` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-263">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="4be25-264">:::no-loc(Blazor):::架構不會嘗試隱藏預設行為，因為它會涉及：</span><span class="sxs-lookup"><span data-stu-id="4be25-264">The :::no-loc(Blazor)::: framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="4be25-265">在架構中建立一鏈特殊案例的因應措施。</span><span class="sxs-lookup"><span data-stu-id="4be25-265">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="4be25-266">目前架構行為的重大變更。</span><span class="sxs-lookup"><span data-stu-id="4be25-266">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4be25-267">HTML 中最合理的對 `null` 等專案是 *空字串* `value` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-267">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="4be25-268">:::no-loc(Blazor):::架構會 `null` 針對雙向系結至值的空字串轉換進行處理 `<select>` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-268">The :::no-loc(Blazor)::: framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4be25-269">:::no-loc(Blazor):::嘗試雙向系結至值時，架構不會自動處理 `null` 空字串轉換 `<select>` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-269">The :::no-loc(Blazor)::: framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="4be25-270">如需詳細資訊，請參閱修正系結 [ `<select>` 至 null 值 (dotnet/aspnetcore #23221) ](https://github.com/dotnet/aspnetcore/pull/23221)。</span><span class="sxs-lookup"><span data-stu-id="4be25-270">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="4be25-271">驗證支援</span><span class="sxs-lookup"><span data-stu-id="4be25-271">Validation support</span></span>

<span data-ttu-id="4be25-272">元件會將 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 使用資料批註的驗證支援附加至串聯的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-272">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="4be25-273">使用資料批註啟用驗證支援需要此明確手勢。</span><span class="sxs-lookup"><span data-stu-id="4be25-273">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="4be25-274">若要使用與資料批註不同的驗證系統，請將取代為 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 自訂的執行。</span><span class="sxs-lookup"><span data-stu-id="4be25-274">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="4be25-275">ASP.NET Core 的執行可在參考來源中進行檢查： [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-275">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="4be25-276">先前的參考來源連結會提供存放庫分支的程式碼 `master` ，代表下一版 ASP.NET Core 的產品單位目前的開發。</span><span class="sxs-lookup"><span data-stu-id="4be25-276">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="4be25-277">若要選取不同版本的分支，請使用 GitHub 分支選取器 (例如 `release/3.1`) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-277">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="4be25-278">:::no-loc(Blazor)::: 會執行兩種類型的驗證：</span><span class="sxs-lookup"><span data-stu-id="4be25-278">:::no-loc(Blazor)::: performs two types of validation:</span></span>

* <span data-ttu-id="4be25-279">*欄位驗證* 是在使用者索引標籤離開欄位時執行。</span><span class="sxs-lookup"><span data-stu-id="4be25-279">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="4be25-280">在欄位驗證期間， <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件會將所有報告的驗證結果與欄位相關聯。</span><span class="sxs-lookup"><span data-stu-id="4be25-280">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="4be25-281">當使用者提交表單時，會執行 *模型驗證* 。</span><span class="sxs-lookup"><span data-stu-id="4be25-281">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="4be25-282">在模型驗證期間， <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件會嘗試根據驗證結果報告的成員名稱來決定欄位。</span><span class="sxs-lookup"><span data-stu-id="4be25-282">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="4be25-283">未與個別成員相關聯的驗證結果會與模型相關聯，而不是與欄位相關聯。</span><span class="sxs-lookup"><span data-stu-id="4be25-283">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="4be25-284">驗證摘要和驗證訊息元件</span><span class="sxs-lookup"><span data-stu-id="4be25-284">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="4be25-285">此 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 元件會摘要所有驗證訊息，類似于 [驗證摘要標記](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)協助程式：</span><span class="sxs-lookup"><span data-stu-id="4be25-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="4be25-286">使用參數輸出特定模型的驗證訊息 `Model` ：</span><span class="sxs-lookup"><span data-stu-id="4be25-286">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="4be25-287"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>元件會顯示特定欄位的驗證訊息，類似于[驗證訊息標記](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)協助程式。</span><span class="sxs-lookup"><span data-stu-id="4be25-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="4be25-288">使用屬性指定驗證欄位 `For` ，並使用命名模型屬性的 lambda 運算式來指定：</span><span class="sxs-lookup"><span data-stu-id="4be25-288">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="4be25-289"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>和 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 元件支援任意屬性。</span><span class="sxs-lookup"><span data-stu-id="4be25-289">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="4be25-290">任何不符合元件參數的屬性都會加入至產生的 `<div>` 或 `<ul>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="4be25-290">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="4be25-291">在應用程式的樣式表單 (或) 中控制驗證訊息的樣式 `wwwroot/css/app.css` `wwwroot/css/site.css` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-291">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="4be25-292">預設 `validation-message` 類別會將驗證訊息的文字色彩設定為紅色：</span><span class="sxs-lookup"><span data-stu-id="4be25-292">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="4be25-293">自訂驗證屬性</span><span class="sxs-lookup"><span data-stu-id="4be25-293">Custom validation attributes</span></span>

<span data-ttu-id="4be25-294">若要在使用 [自訂驗證屬性](xref:mvc/models/validation#custom-attributes)時，確保驗證結果與欄位正確關聯，請 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> 在建立時傳遞驗證內容 <xref:System.ComponentModel.DataAnnotations.ValidationResult> ：</span><span class="sxs-lookup"><span data-stu-id="4be25-294">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="4be25-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> 為 `null`。</span><span class="sxs-lookup"><span data-stu-id="4be25-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="4be25-296">不支援在方法中插入服務以進行驗證 `IsValid` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-296">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="4be25-297">自訂驗證類別屬性</span><span class="sxs-lookup"><span data-stu-id="4be25-297">Custom validation class attributes</span></span>

<span data-ttu-id="4be25-298">在與 CSS 架構（例如 [啟動](https://getbootstrap.com/)程式）整合時，自訂驗證類別名稱很有用。</span><span class="sxs-lookup"><span data-stu-id="4be25-298">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="4be25-299">若要指定自訂驗證類別名稱，請建立衍生自的類別， `FieldCssClassProvider` 並設定實例上的類別 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> ：</span><span class="sxs-lookup"><span data-stu-id="4be25-299">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="4be25-300">:::no-loc(Blazor)::: 資料批註驗證套件</span><span class="sxs-lookup"><span data-stu-id="4be25-300">:::no-loc(Blazor)::: data annotations validation package</span></span>

<span data-ttu-id="4be25-301">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)是使用元件填滿驗證體驗間距的封裝 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-301">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="4be25-302">封裝目前為 *實驗* 性。</span><span class="sxs-lookup"><span data-stu-id="4be25-302">The package is currently *experimental* .</span></span>

> [!NOTE]
> <span data-ttu-id="4be25-303">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)套件在 [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)有最新版本的 *候選版* 。請繼續使用 *實驗* 性發行候選套件。</span><span class="sxs-lookup"><span data-stu-id="4be25-303">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="4be25-304">封裝的元件可能會在未來的版本中移至架構或執行時間。</span><span class="sxs-lookup"><span data-stu-id="4be25-304">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="4be25-305">觀看 [公告 github 存放庫](https://github.com/aspnet/Announcements)、 [Dotnet/aspnetcore GitHub 存放庫](https://github.com/dotnet/aspnetcore)，或本主題一節以取得進一步的更新。</span><span class="sxs-lookup"><span data-stu-id="4be25-305">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="4be25-306">[CompareProperty] 屬性</span><span class="sxs-lookup"><span data-stu-id="4be25-306">[CompareProperty] attribute</span></span>

<span data-ttu-id="4be25-307"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>無法與元件搭配使用， <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 因為它不會將驗證結果與特定成員產生關聯。</span><span class="sxs-lookup"><span data-stu-id="4be25-307">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="4be25-308">這可能會導致欄位層級驗證與在提交時驗證整個模型的行為不一致。</span><span class="sxs-lookup"><span data-stu-id="4be25-308">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="4be25-309">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)*實驗* 性封裝引進了一個額外的驗證屬性， `ComparePropertyAttribute` 它可以解決這些限制。</span><span class="sxs-lookup"><span data-stu-id="4be25-309">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="4be25-310">在 :::no-loc(Blazor)::: 應用程式中， `[CompareProperty]` 是屬性的直接取代 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="4be25-310">In a :::no-loc(Blazor)::: app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="4be25-311">嵌套模型、集合類型和複雜類型</span><span class="sxs-lookup"><span data-stu-id="4be25-311">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="4be25-312">:::no-loc(Blazor)::: 提供使用資料批註搭配內建來驗證表單輸入的支援 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-312">:::no-loc(Blazor)::: provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="4be25-313">不過，只會驗證系結 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 至不是集合型別或複雜型別屬性之表單之模型的最上層屬性。</span><span class="sxs-lookup"><span data-stu-id="4be25-313">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="4be25-314">若要驗證系結模型的整個物件圖形（包括集合型別和複雜型別屬性），請使用 `ObjectGraphDataAnnotationsValidator` *實驗* 性封裝所提供的 [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ：</span><span class="sxs-lookup"><span data-stu-id="4be25-314">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="4be25-315">使用標注模型屬性 `[ValidateComplexType]` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-315">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="4be25-316">在下列模型類別中， `ShipDescription` 類別包含其他資料注釋，可在模型系結至表單時進行驗證：</span><span class="sxs-lookup"><span data-stu-id="4be25-316">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="4be25-317">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="4be25-317">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="4be25-318">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="4be25-318">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="4be25-319">根據表單驗證啟用提交按鈕</span><span class="sxs-lookup"><span data-stu-id="4be25-319">Enable the submit button based on form validation</span></span>

<span data-ttu-id="4be25-320">若要根據表單驗證來啟用和停用 [提交] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="4be25-320">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="4be25-321">您可以使用表單，在 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 元件初始化時指派模型。</span><span class="sxs-lookup"><span data-stu-id="4be25-321">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="4be25-322">驗證內容回呼中的表單， <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 以啟用和停用 [提交] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="4be25-322">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="4be25-323">解除與方法中的事件處理常式的掛鉤 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="4be25-323">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="4be25-324">如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="4be25-324">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="4be25-325">使用時 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> ，也請勿將指派給 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-325">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="4be25-326">在上述範例中，請將設 `formInvalid` 為， `false` 如果：</span><span class="sxs-lookup"><span data-stu-id="4be25-326">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="4be25-327">表單已預先載入有效的預設值。</span><span class="sxs-lookup"><span data-stu-id="4be25-327">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="4be25-328">當表單載入時，您想要啟用 [提交] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="4be25-328">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="4be25-329">上述方法的副作用是，在 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 使用者與任何一個欄位互動之後，元件會填入不正確欄位。</span><span class="sxs-lookup"><span data-stu-id="4be25-329">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="4be25-330">您可以透過下列其中一種方式來解決此案例：</span><span class="sxs-lookup"><span data-stu-id="4be25-330">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="4be25-331">請勿 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 在表單上使用元件。</span><span class="sxs-lookup"><span data-stu-id="4be25-331">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="4be25-332"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>選取 [提交] 按鈕時，讓元件顯示 (例如，在 `HandleValidSubmit`) 方法中。</span><span class="sxs-lookup"><span data-stu-id="4be25-332">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="4be25-333">疑難排解</span><span class="sxs-lookup"><span data-stu-id="4be25-333">Troubleshoot</span></span>

> <span data-ttu-id="4be25-334">InvalidOperationException： EditForm 需要模型參數或 EditCoNtext 參數，但不可同時使用兩者。</span><span class="sxs-lookup"><span data-stu-id="4be25-334">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="4be25-335">確認 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 具有 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **或** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。</span><span class="sxs-lookup"><span data-stu-id="4be25-335">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="4be25-336">請勿將兩者同時用於相同的表單。</span><span class="sxs-lookup"><span data-stu-id="4be25-336">Don't use both for the same form.</span></span>

<span data-ttu-id="4be25-337">將指派 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 給表單時，請確認模型類型已具現化，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="4be25-337">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="4be25-338">其他資源</span><span class="sxs-lookup"><span data-stu-id="4be25-338">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
