---
title: ASP.NET Core Blazor 表單和驗證
author: guardrex
description: 瞭解如何在中使用表單和欄位驗證案例 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: cd613b2b76b8e876786988fdcefc0e7275d3bf53
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056058"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor 表單和驗證

[Daniel Roth](https://github.com/danroth27)、 [Rémi Bourgarel](https://remibou.github.io/)和[Luke Latham](https://github.com/guardrex)

Blazor使用[資料批註](xref:mvc/models/validation)可支援表單和驗證。

下列 `ExampleModel` 類型會使用資料批註來定義驗證邏輯：

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

表單是使用元件所定義的 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。 下列表單會示範一般元素、元件和程式 Razor 代碼：

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

在上述範例中：

* 表單會 `name` 使用型別中定義的驗證來驗證欄位中的使用者輸入 `ExampleModel` 。 此模型會在元件的區塊中建立 `@code` ，並保存在私用欄位中 (`exampleModel`) 。 此欄位會指派給專案的 `Model` 屬性 `<EditForm>` 。
* <xref:Microsoft.AspNetCore.Components.Forms.InputText>元件的系結 `@bind-Value` ：
  * 模型屬性 (`exampleModel.Name`) 至 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 元件的 `Value` 屬性。 如需屬性系結的詳細資訊，請參閱 <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> 。
  * 元件屬性的變更事件委派 <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` 。
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>[驗證程式元件](#validator-components)會附加使用資料批註的驗證支援。
* 此 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 元件會摘要說明驗證訊息。
* `HandleValidSubmit` 當表單成功提交 (通過驗證) 時，就會觸發。

## <a name="built-in-forms-components"></a>內建表單元件

有一組內建元件可用來接收及驗證使用者輸入。 當輸入變更和提交表單時，會驗證輸入。 下表顯示可用的輸入元件。

::: moniker range=">= aspnetcore-5.0"

| 輸入元件 | 呈現為&hellip; |
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

| 輸入元件 | 呈現為&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> `InputRadio`和 `InputRadioGroup` 元件可在 ASP.NET Core 5.0 或更新版本中使用。 如需詳細資訊，請選取此文章的5.0 或更新版本。

::: moniker-end

所有輸入元件（包括）都 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 支援任意屬性。 任何不符合元件參數的屬性都會加入至轉譯的 HTML 元素。

輸入元件提供在欄位變更時驗證的預設行為，包括更新欄位 CSS 類別以反映欄位狀態。 某些元件包含有用的剖析邏輯。 例如，藉 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 由將無法 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 分析的值註冊為驗證錯誤，以正常方式處理無法剖析的值。 可以接受 null 值的類型也支援目標欄位的可 null 性 (例如， `int?`) 。

下列 `Starship` 類型使用較早的屬性和資料批註集來定義驗證邏輯 `ExampleModel` ：

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

在上述範例中， `Description` 是選擇性的，因為沒有任何資料批註存在。

下列表單會使用模型中所定義的驗證來驗證使用者輸入 `Starship` ：

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

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 會建立做為階層式[值](xref:blazor/components/cascading-values-and-parameters)，以追蹤編輯程式的相關中繼資料，包括已修改的欄位和目前的驗證訊息。

將 **either** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **或** 指派給 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。 不支援同時指派，且會產生 **執行階段錯誤** 。

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>提供方便的事件，以進行有效和不正確表單提交：

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 可使用自訂程式碼來觸發驗證和檢查域值。

在下例中︰

* `HandleSubmit`當選取按鈕時，就會執行此方法 **`Submit`** 。
* 藉由呼叫來驗證表單 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> 。
* 會根據驗證結果來執行其他程式碼。 將商務邏輯放在指派給的方法中 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 。

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
> Framework API 不存在，無法從直接清除驗證訊息 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。 因此，我們通常不建議您將驗證訊息新增至 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 表單中的新。 若要管理驗證訊息，請使用 [驗證程式元件](#validator-components) 搭配您的 [商務邏輯驗證程式代碼](#business-logic-validation)，如本文中所述。

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>顯示名稱支援

*本節適用于 .NET 5 候選版 1 (RC1) 或更新版本中的 ASP.NET Core。*

下列內建元件支援使用參數顯示名稱 `DisplayName` ：

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

在下列 `InputDate` 元件範例中：

*  () 的顯示名稱 `DisplayName` 設為 `birthday` 。
* 元件會系結至 `BirthDate` 屬性作為型別 `DateTime` 。

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

如果使用者未提供日期值，驗證錯誤會顯示為：

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>驗證程式元件

驗證程式元件會藉由管理表單的來支援表單驗證 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。

Blazor架構會提供 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件，以根據驗證屬性將驗證支援附加至表單[ (資料批註) ](xref:mvc/models/validation#validation-attributes)。 建立自訂驗證程式元件，在相同頁面上處理不同表單的驗證訊息，或在表單處理的不同步驟中處理相同表單上的驗證訊息，例如用戶端驗證，接著伺服器端驗證。 本節所示的驗證程式元件範例 `CustomValidator` 將用於本文的下列各節：

* [商務邏輯驗證](#business-logic-validation)
* [伺服器驗證](#server-validation)

> [!NOTE]
> 在許多情況下，您可以使用自訂資料批註驗證屬性來取代自訂驗證程式元件。 套用至表單模型的自訂屬性會使用元件來啟用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。 與伺服器端驗證搭配使用時，套用至模型的任何自訂屬性都必須是伺服器上的可執行檔。 如需詳細資訊，請參閱<xref:mvc/models/validation#alternatives-to-built-in-attributes>。

從下列程式建立驗證程式元件 <xref:Microsoft.AspNetCore.Components.ComponentBase> ：

* 表單的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 是元件的串聯 [參數](xref:blazor/components/cascading-values-and-parameters) 。
* 當驗證程式元件初始化時， <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 會建立新的來維護目前的表單錯誤清單。
* 當表單元件中的開發人員程式碼呼叫方法時，訊息存放區會接收錯誤 `DisplayErrors` 。 錯誤會傳遞至中的 `DisplayErrors` 方法 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) 。 在字典中，索引鍵是有一或多個錯誤的表單欄位名稱。 值是錯誤清單。
* 當發生下列任何一種情況時，就會清除訊息：
  * <xref:Microsoft.AspNetCore.Components.Forms.EditContext>當引發事件時，會在上要求驗證 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> 。 所有錯誤都會清除。
  * 當引發事件時，表單中的欄位 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 就會變更。 只會清除欄位的錯誤。
  * `ClearErrors`方法是由開發人員程式碼呼叫。 所有錯誤都會清除。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
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

## <a name="business-logic-validation"></a>商務邏輯驗證

您可以使用接收字典中的表單錯誤的 [驗證程式元件](#validator-components) 來完成商務邏輯驗證。

在下例中︰

* 本文的「 `CustomValidator` [驗證程式元件](#validator-components) 」一節中會使用此元件。
* `Description`如果使用者選取 [出 `Defense` 貨分類] () ，則驗證會要求出貨的描述 () 的值 `Classification` 。

在元件中設定驗證訊息時，會將它們新增至驗證程式， <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 並顯示在 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> ：

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
> 除了使用 [驗證元件](#validator-components)之外，也可以使用資料批註驗證屬性。 套用至表單模型的自訂屬性會使用元件來啟用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。 與伺服器端驗證搭配使用時，屬性必須是伺服器上的可執行檔。 如需詳細資訊，請參閱<xref:mvc/models/validation#alternatives-to-built-in-attributes>。

## <a name="server-validation"></a>伺服器驗證

您可以使用伺服器驗證程式 [元件](#validator-components)來完成伺服器驗證：

* 使用元件，處理表單中的用戶端驗證 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。
* 當表單通過用戶端驗證時 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> ， (稱為) ，請將傳送 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> 至後端伺服器 API 以進行表單處理。
* 伺服器上的進程模型驗證。
* 伺服器 API 包含內建的架構資料批註驗證，以及開發人員所提供的自訂驗證邏輯。 如果驗證是在伺服器上通過，請處理表單並將成功狀態碼傳回 ( *200-確定* ) 。 如果驗證失敗，會傳回失敗狀態碼 ( *400-錯誤的要求* ) 和欄位驗證錯誤。
* 請停用成功的表單，或顯示錯誤。

下列範例是根據：

* 主控 Blazor 的[ Blazor 專案範本](xref:blazor/hosting-models#blazor-webassembly)所建立的裝載方案。 此範例可以與 Blazor [安全性和 Identity 檔](xref:blazor/security/webassembly/index#implementation-guidance)中所述的任何安全託管解決方案搭配使用。
* 上述內 [建表單元件](#built-in-forms-components)區段中的 *Starfleet Starship 資料庫* 表單範例。
* Blazor架構的 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件。
* 在 `CustomValidator` [ [驗證程式元件](#validator-components) ] 區段中顯示的元件。

在下列範例中，伺服器 API 會驗證是否針對出貨的描述提供值 (`Description`) 如果使用者選取 [出 `Defense` 貨分類] (`Classification`) 。

將 `Starship` 模型放入方案的專案中， `Shared` 讓用戶端和伺服器應用程式都可以使用此模型。 由於模型需要資料批註，因此請將的封裝參考新增 [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) 至 `Shared` 專案的專案檔：

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

若要判斷封裝的最新非預覽版本，請參閱套件 **版本歷程記錄** ，網址為 [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)。

在伺服器 API 專案中，加入控制器來處理 starship 驗證要求 (`Controllers/StarshipValidation.cs`) 並傳回失敗的驗證訊息：

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
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

當伺服器上發生模型系結驗證錯誤時， [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) 通常會傳回 [預設錯誤的要求回應](xref:web-api/index#default-badrequest-response) <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 。 回應所包含的資料超過驗證錯誤，如下列範例所示，當 *Starfleet Starship 資料庫* 表單的所有欄位都未送出，而且表單驗證失敗時：

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

如果伺服器 API 傳回先前的預設 JSON 回應，用戶端可能會剖析回應以取得節點的子系 `errors` 。 不過，剖析檔案並不方便。 在呼叫之後剖析 JSON 需要額外的程式碼 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> ，才能產生 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) 表單驗證錯誤處理的錯誤。 在理想的情況下，伺服器 API 應該只傳回驗證錯誤：

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

若要修改伺服器 API 的回應，使其只傳回驗證錯誤，請變更在中標注的動作所叫用的委派 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> `Startup.ConfigureServices` 。 針對 () 的 API 端點 `/StarshipValidation` ，請 <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> 使用傳回 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> 。 針對任何其他 API 端點，請以新的傳回物件結果來保留預設行為 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> ：

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

如需詳細資訊，請參閱<xref:web-api/handle-errors#validation-failure-error-response>。

在用戶端專案中，加入 [ [驗證程式元件](#validator-components) ] 區段中顯示的驗證程式元件。

在用戶端專案中，會更新 *Starfleet Starship 資料庫* 表單，以顯示伺服器驗證錯誤並提供 `CustomValidator` 元件的協助。 當伺服器 API 傳回驗證訊息時，就會將它們新增至 `CustomValidator` 元件的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 。 表單的可顯示錯誤， <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 表單的格式 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 如下：

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
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
> 您可以使用資料批註驗證屬性作為 [驗證元件](#validator-components)的替代方案。 套用至表單模型的自訂屬性會使用元件來啟用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。 與伺服器端驗證搭配使用時，屬性必須是伺服器上的可執行檔。 如需詳細資訊，請參閱<xref:mvc/models/validation#alternatives-to-built-in-attributes>。

> [!NOTE]
> 本節中的伺服器端驗證方法適用于此檔集中的任何裝載 Blazor WebAssembly 解決方案範例：
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity 伺服器](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>以輸入事件為基礎的 InputText

使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 元件建立使用事件的自訂群組件， `input` 而不是 `change` 事件。

在下列範例中， `CustomInputText` 元件會繼承架構的 `InputText` 元件，並將事件系結 (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) 設定至 `oninput` 事件。

`Shared/CustomInputText.razor`:

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

`CustomInputText`元件可以使用於任何位置 <xref:Microsoft.AspNetCore.Components.Forms.InputText> ：

`Pages/TestForm.razor`:

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

## <a name="radio-buttons"></a>選項按鈕

::: moniker range=">= aspnetcore-5.0"

使用元件搭配 `InputRadio` `InputRadioGroup` 元件來建立選項按鈕群組。 在下列範例中，屬性會加入至 `Starship` 內 [建表單元件](#built-in-forms-components) 區段中所述的模型：

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

將下列 `enums` 程式新增至應用程式。 建立新的檔案以保存， `enums` 或將新增 `enums` 至檔案 `Starship.cs` 。 讓 `enums` `Starship` 模型和 *Starfleet Starship 資料庫* 形式可存取：

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

更新內 [建表單元件](#built-in-forms-components)一節中所述的 *Starfleet Starship 資料庫* 表單。 新增要產生的元件：

* 出貨製造商的選項按鈕群組。
* 出貨色彩和引擎的嵌套選項按鈕群組。

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
> 如果 `Name` 省略，則 `InputRadio` 會依最新的上階來群組元件。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

當您在表單中使用選項按鈕時，資料系結的處理方式會與其他元素不同，因為選項按鈕會評估為群組。 每個選項按鈕的值都是固定的，但選項按鈕群組的值是選取之選項按鈕的值。 下列範例示範如何執行：

* 處理選項按鈕群組的資料系結。
* 使用自訂群組件的支援驗證 `InputRadio` 。

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

以下會 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 使用上述 `InputRadio` 元件來取得並驗證使用者的評等：

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

## <a name="binding-select-element-options-to-c-object-null-values"></a>`<select>`C # 物件值的繫結項目選項 `null`

`<select>`因為下列原因，所以無法以 c # 物件值表示元素選項值 `null` ：

* HTML 屬性不能有 `null` 值。 HTML 中最接近的對等專案 `null` ，不是元素中的 html `value` 屬性 `<option>` 。
* 當您選取 `<option>` 不含 `value` 屬性的時，瀏覽器會將此值視為該元素的 *文字內容* `<option>` 。

Blazor架構不會嘗試隱藏預設行為，因為它會涉及：

* 在架構中建立一鏈特殊案例的因應措施。
* 目前架構行為的重大變更。

::: moniker range=">= aspnetcore-5.0"

HTML 中最合理的對 `null` 等專案是 *空字串* `value` 。 Blazor架構會 `null` 針對雙向系結至值的空字串轉換進行處理 `<select>` 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor嘗試雙向系結至值時，架構不會自動處理 `null` 空字串轉換 `<select>` 。 如需詳細資訊，請參閱修正系結 [ `<select>` 至 null 值 (dotnet/aspnetcore #23221) ](https://github.com/dotnet/aspnetcore/pull/23221)。

::: moniker-end

## <a name="validation-support"></a>驗證支援

元件會將 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 使用資料批註的驗證支援附加至串聯的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。 使用資料批註啟用驗證支援需要此明確手勢。 若要使用與資料批註不同的驗證系統，請將取代為 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 自訂的執行。 ASP.NET Core 的執行可在參考來源中進行檢查： [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) 。 先前的參考來源連結會提供存放庫分支的程式碼 `master` ，代表下一版 ASP.NET Core 的產品單位目前的開發。 若要選取不同版本的分支，請使用 GitHub 分支選取器 (例如 `release/3.1`) 。

Blazor 會執行兩種類型的驗證：

* *欄位驗證* 是在使用者索引標籤離開欄位時執行。 在欄位驗證期間， <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件會將所有報告的驗證結果與欄位相關聯。
* 當使用者提交表單時，會執行 *模型驗證* 。 在模型驗證期間， <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 元件會嘗試根據驗證結果報告的成員名稱來決定欄位。 未與個別成員相關聯的驗證結果會與模型相關聯，而不是與欄位相關聯。

### <a name="validation-summary-and-validation-message-components"></a>驗證摘要和驗證訊息元件

此 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 元件會摘要所有驗證訊息，類似于 [驗證摘要標記](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)協助程式：

```razor
<ValidationSummary />
```

使用參數輸出特定模型的驗證訊息 `Model` ：
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>元件會顯示特定欄位的驗證訊息，類似于[驗證訊息標記](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)協助程式。 使用屬性指定驗證欄位 `For` ，並使用命名模型屬性的 lambda 運算式來指定：

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>和 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 元件支援任意屬性。 任何不符合元件參數的屬性都會加入至產生的 `<div>` 或 `<ul>` 元素中。

在應用程式的樣式表單 (或) 中控制驗證訊息的樣式 `wwwroot/css/app.css` `wwwroot/css/site.css` 。 預設 `validation-message` 類別會將驗證訊息的文字色彩設定為紅色：

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>自訂驗證屬性

若要在使用 [自訂驗證屬性](xref:mvc/models/validation#custom-attributes)時，確保驗證結果與欄位正確關聯，請 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> 在建立時傳遞驗證內容 <xref:System.ComponentModel.DataAnnotations.ValidationResult> ：

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
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> 為 `null`。 不支援在方法中插入服務以進行驗證 `IsValid` 。

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>自訂驗證類別屬性

在與 CSS 架構（例如 [啟動](https://getbootstrap.com/)程式）整合時，自訂驗證類別名稱很有用。 若要指定自訂驗證類別名稱，請建立衍生自的類別， `FieldCssClassProvider` 並設定實例上的類別 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> ：

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

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor 資料批註驗證套件

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)是使用元件填滿驗證體驗間距的封裝 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。 封裝目前為 *實驗* 性。

> [!NOTE]
> [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)套件在 [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)有最新版本的 *候選版* 。請繼續使用 *實驗* 性發行候選套件。 封裝的元件可能會在未來的版本中移至架構或執行時間。 觀看 [公告 github 存放庫](https://github.com/aspnet/Announcements)、 [Dotnet/aspnetcore GitHub 存放庫](https://github.com/dotnet/aspnetcore)，或本主題一節以取得進一步的更新。

### <a name="compareproperty-attribute"></a>[CompareProperty] 屬性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>無法與元件搭配使用， <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 因為它不會將驗證結果與特定成員產生關聯。 這可能會導致欄位層級驗證與在提交時驗證整個模型的行為不一致。 [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)*實驗* 性封裝引進了一個額外的驗證屬性， `ComparePropertyAttribute` 它可以解決這些限制。 在 Blazor 應用程式中， `[CompareProperty]` 是屬性的直接取代 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 。

### <a name="nested-models-collection-types-and-complex-types"></a>嵌套模型、集合類型和複雜類型

Blazor 提供使用資料批註搭配內建來驗證表單輸入的支援 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 。 不過，只會驗證系結 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 至不是集合型別或複雜型別屬性之表單之模型的最上層屬性。

若要驗證系結模型的整個物件圖形（包括集合型別和複雜型別屬性），請使用 `ObjectGraphDataAnnotationsValidator` *實驗* 性封裝所提供的 [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ：

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

使用標注模型屬性 `[ValidateComplexType]` 。 在下列模型類別中， `ShipDescription` 類別包含其他資料注釋，可在模型系結至表單時進行驗證：

`Starship.cs`:

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

`ShipDescription.cs`:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>根據表單驗證啟用提交按鈕

若要根據表單驗證來啟用和停用 [提交] 按鈕：

* 您可以使用表單，在 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 元件初始化時指派模型。
* 驗證內容回呼中的表單， <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 以啟用和停用 [提交] 按鈕。
* 解除與方法中的事件處理常式的掛鉤 `Dispose` 。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

> [!NOTE]
> 使用時 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> ，也請勿將指派給 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。

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

在上述範例中，請將設 `formInvalid` 為， `false` 如果：

* 表單已預先載入有效的預設值。
* 當表單載入時，您想要啟用 [提交] 按鈕。

上述方法的副作用是，在 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 使用者與任何一個欄位互動之後，元件會填入不正確欄位。 您可以透過下列其中一種方式來解決此案例：

* 請勿 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 在表單上使用元件。
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>選取 [提交] 按鈕時，讓元件顯示 (例如，在 `HandleValidSubmit`) 方法中。

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

## <a name="troubleshoot"></a>疑難排解

> InvalidOperationException： EditForm 需要模型參數或 EditCoNtext 參數，但不可同時使用兩者。

確認 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 具有 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **或** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 。 請勿將兩者同時用於相同的表單。

將指派 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 給表單時，請確認模型類型已具現化，如下列範例所示：

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>其他資源

* <xref:blazor/file-uploads>

::: moniker-end
