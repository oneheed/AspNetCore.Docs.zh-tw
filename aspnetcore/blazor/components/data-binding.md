---
title: ASP.NET Core Blazor 資料系結
author: guardrex
description: 瞭解應用程式中元件和 DOM 元素的資料系結功能 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
ms.openlocfilehash: 67a63f1b4f705a4857dea2e6d1a942d4f21469f5
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057092"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor 資料系結

[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Steve Sanderson](https://github.com/SteveSandersonMS)

Razor 元件會透過以 [`@bind`](xref:mvc/views/razor#bind) 欄位、屬性或運算式值命名的 HTML 元素屬性，提供資料系結功能 Razor 。

下列範例會將專案系結 `<input>` 至 `currentValue` 欄位，並將元素系結 `<input>` 至 `CurrentValue` 屬性：

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

當其中一個元素失去焦點時，就會更新其系結欄位或屬性。

只有在轉譯元件時，才會更新 UI 中的文字方塊，而不是回應變更欄位或屬性的值。 由於元件會在事件處理常式程式碼執行之後自行轉譯，因此在觸發事件處理常式之後，欄位和屬性更新 *通常* 會立即反映在 UI 中。

使用 [`@bind`](xref:mvc/views/razor#bind) 與 `CurrentValue` 屬性 (`<input @bind="CurrentValue" />`) 基本上等同于下列專案：

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

轉譯元件時， `value` 輸入元素的會來自 `CurrentValue` 屬性。 當使用者在文字方塊中輸入並變更元素焦點時， `onchange` 就會引發事件，並將 `CurrentValue` 屬性設定為變更的值。 事實上，程式碼產生比起的複雜，因為它會 [`@bind`](xref:mvc/views/razor#bind) 處理型別轉換的執行情況。 在主體中，會將 [`@bind`](xref:mvc/views/razor#bind) 運算式的目前值與屬性產生關聯 `value` ，並使用已註冊的處理常式來處理變更。

將屬性或欄位系結到其他事件，也可以包含 `@bind:event` 具有參數的屬性 `event` 。 下列範例會系結 `CurrentValue` 事件上的屬性 `oninput` ：

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

不同于 `onchange` 當專案失去焦點時引發的專案，會 `oninput` 在文字方塊的值變更時引發。

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

屬性系結會區分大小寫：

* `@bind` 有效。
* `@Bind` 和 `@BIND` 無效。

## <a name="unparsable-values"></a>無法剖析的值

當使用者提供無法剖析的值給資料系結專案時，會在觸發 bind 事件時，將無法剖析的值自動還原為先前的值。

考慮下列案例：

* 專案 `<input>` 會系結至 `int` 值為的類型 `123` ：

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* 使用者會將專案的值更新為 `123.45` 頁面中的，並變更元素焦點。

在上述案例中，元素的值會還原為 `123` 。 當值 `123.45` 被拒絕時，若要改用的原始值 `123` ，使用者瞭解其值不會被接受。

根據預設，系結會套用至元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`) 。 用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 來觸發不同事件的系結。 針對 `oninput` () 的事件 `@bind:event="oninput"` ，回復會在引進未剖析值的任何按鍵之後進行。 以系結類型為目標的 `oninput` 事件時 `int` ，使用者無法輸入 `.` 字元。 `.`系統會立即移除字元，因此使用者會收到只允許整數的立即回應。 在某些情況下，還原事件的值 `oninput` 並不理想，例如，當使用者應該清除無法剖析的 `<input>` 值時。 替代方案包括：

* 請勿使用 `oninput` 事件。 使用預設 `onchange` 事件 (只指定 `@bind="{PROPERTY OR FIELD}"`) ，其中不會還原無效值，直到元素失去焦點為止。
* 系結至可為 null 的型別（例如或）， `int?` `string` 並提供自訂邏輯來處理不正確專案。
* 使用 [表單驗證元件](xref:blazor/forms-validation)，例如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 。 表單驗證元件具有內建的支援，可管理不正確輸入。 如需詳細資訊，請參閱<xref:blazor/forms-validation>。 表單驗證元件：
  * 允許使用者提供不正確輸入，並在相關聯的上接收驗證錯誤 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。
  * 在 UI 中顯示驗證錯誤，而不會干擾使用者輸入其他 webform 資料。

## <a name="format-strings"></a>格式字串

資料系結 <xref:System.DateTime> 使用的格式字串 `@bind:format` 。 現在無法使用其他格式運算式，例如貨幣或數位格式。

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

在上述程式碼中， `<input>` 元素的欄位類型 (`type`) 預設為 `text` 。 `@bind:format` 支援系結下列 .NET 類型：

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format`屬性會指定要套用至元素之的日期格式 `value` `<input>` 。 當事件發生時，也會使用此格式來剖析該值 `onchange` 。

`date`由於 Blazor 具有格式化日期的內建支援，因此不建議指定欄位類型的格式。 在建議的情況下， `yyyy-MM-dd` 如果格式是以欄位類型提供，只使用日期格式讓系結正常運作 `date` ：

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a>使用元件參數進行系結

常見的案例是將子元件中的屬性系結至其父系中的屬性。 此案例稱為 *連鎖* 系結，因為有多個層級的系結同時發生。

[元件參數](xref:blazor/components/index#component-parameters) 允許使用語法來系結父元件的屬性 `@bind-{PROPERTY}` 。

連結系結無法使用 [`@bind`](xref:mvc/views/razor#bind) 子元件中的語法來執行。 必須個別指定事件處理常式和值，以支援從子元件的父系更新屬性。

父元件仍會利用 [`@bind`](xref:mvc/views/razor#bind) 語法來設定子元件的資料系結。

下列 `Child` 元件 (`Shared/Child.razor`) 具有 `Year` 元件參數和 `YearChanged` 回呼：

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

回呼 (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) 必須命名為元件參數名稱，後面接著 " `Changed` " 尾碼 (`{PARAMETER NAME}Changed`) 。 在上述範例中，回呼的名稱為 `YearChanged` 。 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 使用提供的引數叫用與系結相關聯的委派，並分派已變更屬性的事件通知。

在下列 `Parent` 元件 (`Parent.razor`) 中，欄位會系結 `year` 至 `Year` 子元件的參數：

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

`Year`參數是可系結的，因為它具有 `YearChanged` 符合參數類型的伴隨事件 `Year` 。

依照慣例，屬性可以藉由包含指派給處理常式的屬性，系結至對應的事件處理常式 `@bind-{PROPERTY}:event` 。 `<Child @bind-Year="year" />` 相當於撰寫：

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

在更複雜的真實世界範例中，下列 `PasswordField` 元件 (`PasswordField.razor`) ：

* 將 `<input>` 元素的值設定為 `password` 欄位。
* 將屬性的變更公開 `Password` 至父代元件 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) ，該元件會傳入子系欄位的目前值 `password` 做為其引數。
* 使用 `onclick` 事件來觸發 `ToggleShowPassword` 方法。 如需詳細資訊，請參閱<xref:blazor/components/event-handling>。

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

`PasswordField`元件是在另一個元件中使用：

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

在叫用系結委派的方法中執行檢查或陷阱錯誤。 下列範例會在密碼值中使用空格時，為使用者提供立即的意見反應：

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

如需 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 的詳細資訊，請參閱 <xref:blazor/components/event-handling#eventcallback>。

## <a name="bind-across-more-than-two-components"></a>跨兩個以上的元件進行系結

您可以透過任意數目的嵌套元件進行系結，但您必須遵守單向的資料流程：

* 變更通知會在階層中 *往上流動*。
* 新的參數值會在階層中 *往下流動*。

常見和建議的方法是只將基礎資料儲存在父元件中，以避免任何必須更新狀態的混淆。

下列元件示範上述概念：

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

如需適合在不需要嵌套的元件之間共用記憶體資料的替代方法，請參閱本文的 *記憶體內部狀態容器服務* 一節 <xref:blazor/state-management> 。

## <a name="additional-resources"></a>其他資源

* [系結至表單中的選項按鈕](xref:blazor/forms-validation#radio-buttons)
* [將 `<select>` 元件選項系結至 `null` 表單中的 c # 物件值](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
