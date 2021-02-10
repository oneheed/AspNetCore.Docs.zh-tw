---
title: 建立和使用 ASP.NET Core Razor 元件
author: guardrex
description: 瞭解如何建立和使用 Razor 元件，包括如何系結至資料、處理事件，以及管理元件生命週期。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 111512916cb7f0a4fc1f17648e2f9c69e366dff3
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107047"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>建立和使用 ASP.NET Core Razor 元件

[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)、 [Scott Addie](https://github.com/scottaddie)和[Tobias Bartsch](https://www.aveo-solutions.com/)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

Blazor 應用程式是使用 *元件* 建立的。 元件是獨立的使用者介面區塊， (UI) ，例如頁面、對話方塊或表單。 元件包含 HTML 標籤以及插入資料或回應 UI 事件所需的處理邏輯。 元件具有彈性和輕量。 這些專案可以在專案之間進行嵌套、重複使用及共用。

## <a name="component-classes"></a>元件類別

元件會 [Razor](xref:mvc/views/razor) `.razor` 使用 c # 和 HTML 標籤的組合，在元件檔 () 中實作為元件。 中的元件 Blazor 正式參考為 *Razor 元件*。

### <a name="razor-syntax"></a>Razor 語法

Razor 應用程式中的元件會 Blazor 廣泛使用 Razor 語法。 如果您不熟悉 Razor 標記語言，建議您先閱讀[ Razor ASP.NET Core 的語法參考](xref:mvc/views/razor)，再繼續進行。

存取語法上的內容時 Razor ，請特別注意下列各節：

* [](xref:mvc/views/razor#directives) `@` 指示詞：前置詞保留關鍵字，通常會變更元件標記的剖析或運作方式。
* [](xref:mvc/views/razor#directive-attributes) `@` 指示詞屬性：-前置詞，通常會變更元件元素剖析或運作的方式。

### <a name="names"></a>名稱

元件的名稱必須以大寫字元開頭。 例如， `MyCoolComponent.razor` 有效，且 `myCoolComponent.razor` 無效。

### <a name="routing"></a>路由

中的路由傳送 Blazor 可透過提供應用程式中每個可存取元件的路由範本來達成。 當編譯具有指示詞的檔案時 Razor [`@page`][9] ，產生的類別會 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由範本。 在執行時間，路由器會尋找具有的元件類別 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> ，並轉譯任何元件具有符合所要求 URL 的路由範本。 如需詳細資訊，請參閱<xref:blazor/fundamentals/routing>。

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>標記

元件的 UI 是使用 HTML 來定義。 動態轉譯邏輯 (例如，迴圈、條件、運算式) 是使用稱為的內嵌 c # 語法來新增 *Razor* 。 當編譯應用程式時，會將 HTML 標籤和 c # 轉譯邏輯轉換成元件類別。 產生之類別的名稱符合檔案名。

元件類別的成員是在區塊中定義 [`@code`][1] 。 在 [`@code`][1] 區塊中，[元件狀態] ([屬性]、[欄位]) 是以事件處理方法或定義其他元件邏輯的方法所指定。 允許一個以上 [`@code`][1] 的區塊。

元件成員可以使用以開頭的 c # 運算式作為元件轉譯邏輯的一部分 `@` 。 例如，c # 欄位的呈現方式是在功能變數名稱前面加上 `@` 。 下列範例會評估和轉譯：

* `headingFontStyle` 至的 CSS 屬性值 `font-style` 。
* `headingText` 至元素的內容 `<h1>` 。

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

在元件一開始呈現之後，元件會重新產生其轉譯樹狀結構，以回應事件。 Blazor 然後比較新的轉譯樹狀結構與上一個樹狀結構，並將任何修改套用至瀏覽器的檔物件模型 (DOM) 。 提供其他詳細資料 <xref:blazor/components/rendering> 。

元件是一般的 c # 類別，可放在專案內的任何位置。 產生網頁的元件通常位於資料夾中 `Pages` 。 非頁面元件通常會放在資料夾中， `Shared` 或加入至專案的自訂資料夾中。

### <a name="namespaces"></a>命名空間

一般而言，元件的命名空間是從應用程式的根命名空間和元件在應用程式中)  (資料夾的位置衍生。 如果應用程式的根命名空間為 `BlazorSample` ，而且 `Counter` 元件位於 `Pages` 資料夾中：

* `Counter`元件的命名空間為 `BlazorSample.Pages` 。
* 元件的完整類型名稱為 `BlazorSample.Pages.Counter` 。

若為保存元件的自訂資料夾，請將指示詞新增 [`@using`][2] 至父元件或應用程式的檔案 `_Imports.razor` 。 下列範例會讓資料夾中的元件 `Components` 可供使用：

```razor
@using BlazorSample.Components
```

元件也可以使用其完整名稱來參考，而不需要指示詞 [`@using`][2] ：

```razor
<BlazorSample.Components.MyComponent />
```

使用撰寫之元件的命名空間 Razor 是以優先權順序) 的 (為基礎：

* [`@namespace`][8] 檔案 (中的指定 Razor `.razor`) 標記 (`@namespace BlazorSample.MyNamespace`) 。
* 專案檔中的專案會 `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`) 。
* 專案名稱（取自專案檔的檔案名 (`.csproj`) ），以及從專案根目錄到元件的路徑。 例如，架構會將 `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) 解析為命名空間 `BlazorSample.Pages` 。 元件遵循 c # 名稱系結規則。 針對 `Index` 此範例中的元件，範圍中的元件都是所有的元件：
  * 在相同的資料夾中 `Pages` 。
  * 專案根目錄中未明確指定不同命名空間的元件。

> [!NOTE]
> `global::`不支援資格。
>
> 使用別名語句匯入元件 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (例如， `@using Foo = Bar` 不支援) 。
>
> 不支援部分限定名稱。 例如， `@using BlazorSample` 不支援新增和參考 `NavMenu` 元件 (`NavMenu.razor`) `<Shared.NavMenu></Shared.NavMenu>` 。

### <a name="partial-class-support"></a>部分類別支援

Razor 元件會以部分類別的形式產生。 Razor 您可以使用下列其中一種方法來撰寫元件：

* C # 程式碼是以 [`@code`][1] 單一檔案中的 HTML 標籤和程式碼區塊來定義 Razor 。 Blazor 範本會 Razor 使用這種方法來定義其元件。
* C # 程式碼會放在定義為部分類別的程式碼後置檔案中。

下列範例顯示 `Counter` [`@code`][1] 從範本產生的應用程式中有區塊的預設元件 Blazor 。 HTML 標籤、程式 Razor 代碼和 c # 程式碼位於相同的檔案中：

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

`Counter`元件也可以使用具有部分類別的程式碼後端檔案來建立：

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

視需要將任何必要的命名空間加入至部分類別檔案。 元件所使用的一般命名空間 Razor 包括：

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2] 檔案中的指示詞 `_Imports.razor` 只會套用至檔案 Razor (`.razor`) ，而非 c # 檔案 (`.cs`) 。

### <a name="specify-a-base-class"></a>指定基類

指示詞 [`@inherits`][6] 可以用來指定元件的基類。 下列範例會顯示元件如何繼承基類， `BlazorRocksBase` 以提供元件的屬性和方法。 基類應衍生自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 。

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a>使用元件

元件可以使用 HTML 元素語法來宣告它們，以包含其他元件。 使用元件的標記看起來像是 HTML 標籤，其中標籤名稱是元件類型。

中的下列標記會 `Pages/Index.razor` 呈現 `HeadingComponent` 實例：

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

如果元件包含的 HTML 元素具有不符合元件名稱的大寫第一個字母，則會發出警告，指出元素有未預期的名稱。 新增 [`@using`][2] 元件命名空間的指示詞會讓元件可供使用，以解決警告。

## <a name="parameters"></a>參數

### <a name="route-parameters"></a>路由參數

元件可以從指示詞中提供的路由範本接收路由參數 [`@page`][9] 。 路由器會使用路由參數來填入對應的元件參數。

::: moniker range=">= aspnetcore-5.0"

支援選擇性參數。 在下列範例中， `text` 選擇性參數會將路由區段的值指派給元件的 `Text` 屬性。 如果區段不存在，的值 `Text` 會設定為 `fantastic` 。

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

不支援選擇性參數，因此 [`@page`][9] 在上述範例中會套用兩個指示詞。 第一個可讓您在不使用參數的情況下流覽至元件。 第二個指示詞會 [`@page`][9] 接收 `{text}` route 參數，並將值指派給 `Text` 屬性。

::: moniker-end

如需捕捉所有路由參數的詳細資訊 (`{*pageRoute}`) ，以在多個資料夾界限之間取得路徑，請參閱 <xref:blazor/fundamentals/routing#catch-all-route-parameters> 。

### <a name="component-parameters"></a>元件參數

元件可以有 *元件參數*，這些參數是使用元件類別上的公用簡單或複雜屬性（ [ `[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute)）來定義的。 使用這些屬性來指定標記中元件的引數。

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

您可以將預設值指派給元件參數：

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

在範例應用程式的下列範例中，會 `ParentComponent` 設定的 `Title` 屬性值 `ChildComponent` 。

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

依照慣例，由 c # 程式碼所組成的屬性值會使用[ Razor 的保留 `@` 符號](xref:mvc/views/razor#razor-syntax)指派給參數：

* 父欄位或屬性： `Title="@{FIELD OR PROPERTY}` ，其中預留位置 `{FIELD OR PROPERTY}` 是父元件的 c # 欄位或屬性。
* 方法的結果： `Title="@{METHOD}"` ，其中預留位置 `{METHOD}` 是父元件的 c # 方法。
* [隱含或明確運算式](xref:mvc/views/razor#implicit-razor-expressions)： `Title="@({EXPRESSION})"` ，其中預留位置 `{EXPRESSION}` 是 c # 運算式。
  
如需詳細資訊，請參閱[ Razor ASP.NET Core 的語法參考](xref:mvc/views/razor)。

> [!WARNING]
> 請勿建立會寫入其本身 *元件參數* 的元件，而是改用私用欄位。 如需詳細資訊，請參閱 [覆寫的參數](#overwritten-parameters) 一節。

## <a name="child-content"></a>子內容

元件可以設定另一個元件的內容。 指派元件會在指定接收元件的標記之間提供內容。

在下列範例中， `ChildComponent` 有一個 `ChildContent` 代表的屬性 <xref:Microsoft.AspNetCore.Components.RenderFragment> ，代表要轉譯的 UI 區段。 的值位於 `ChildContent` 應呈現內容的元件標記中。 的值 `ChildContent` 是從父元件接收，並在啟動程式面板內轉譯 `panel-body` 。

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> 接收內容的屬性 <xref:Microsoft.AspNetCore.Components.RenderFragment> 必須依照慣例命名 `ChildContent` 。

`ParentComponent`範例應用程式中的會將 `ChildComponent` 內容放在標記內，以提供轉譯的內容 `<ChildComponent>` 。

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

由於轉譯 Blazor 子內容的方式， `for` 如果在子元件的內容中使用遞增迴圈變數，則在迴圈內轉譯元件需要本機索引變數：
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> 或者，使用 `foreach` 迴圈搭配 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> ：
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

如需如何 <xref:Microsoft.AspNetCore.Components.RenderFragment> 使用做為元件 UI 範本的詳細資訊 Razor ，請參閱下列文章：

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a>屬性展開和任意參數

元件除了元件的宣告參數之外，還可以捕獲和轉譯其他屬性。 您可以在字典中捕捉其他屬性，然後在使用指示詞轉譯元件時， *splatted* 至元素 [`@attributes`][3] Razor 。 當您定義的元件會產生支援各種自訂專案的標記專案時，此案例很有用。 例如，針對 `<input>` 支援許多參數的來個別定義屬性可能相當繁瑣。

在下列範例中，第一個專案 `<input>` (`id="useIndividualParams"`) 使用個別的元件參數，而第二個 `<input>` 元素 (`id="useAttributesDict"`) 使用屬性展開：

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

參數的類型必須 `IEnumerable<KeyValuePair<string, object>>` `IReadOnlyDictionary<string, object>` 使用字串索引鍵來執行。

`<input>`使用這兩種方法的轉譯元素相同：

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

若要接受任意屬性，請使用屬性設定為[ `[Parameter]` 的屬性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)來定義元件參數 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> `true` ：

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>上的屬性 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 可讓參數符合所有不符合任何其他參數的屬性。 元件只能用來定義單一參數 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 。 搭配使用的屬性類型， <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 必須可從 `Dictionary<string, object>` 使用字串索引鍵來指派。 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>` 也是此案例中的選項。

[`@attributes`][3]相對於元素屬性位置的位置是很重要的。 在專案 [`@attributes`][3] 上 splatted 時，會由右至左處理屬性 (最後一個) 。 請考慮下列使用元件的元件範例 `Child` ：

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child`元件的 `extra` 屬性會設定為的右邊 [`@attributes`][3] 。 `Parent` `<div>` `extra="5"` 由於屬性是由右至左 (最後一個) 來處理，因此，在透過其他屬性傳遞時，會包含該元件的所呈現內容。

```html
<div extra="5" />
```

在下列範例中，和的順序 `extra` [`@attributes`][3] 會在 `Child` 元件的中反轉 `<div>` ：

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`<div>` `Parent` `extra="10"` 當透過其他屬性傳遞時，元件中所呈現的會包含：

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>捕獲元件的參考

元件參考提供一個參考元件實例的方式，讓您可以將命令發出至該實例，例如 `Show` 或 `Reset` 。 若要捕獲元件參考：

* 將 [`@ref`][4] 屬性新增至子元件。
* 使用與子元件相同的類型來定義欄位。

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

轉譯元件時，此 `loginDialog` 欄位會填入 `CustomLoginDialog` 子元件實例。 然後，您可以在元件實例上叫用 .NET 方法。

> [!IMPORTANT]
> `loginDialog`變數只會在呈現元件之後填入，且其輸出會包含 `MyLoginDialog` 元素。 在呈現元件之前，不需要參考任何專案。
>
> 若要在元件完成轉譯之後操作元件參考，請使用[ `OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)。
>
> 若要在事件處理常式中使用參考變數，請使用 lambda 運算式，或在[ `OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)中指派事件處理常式委派。 這可確保在指派事件處理常式之前，會先指派參考變數。
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

若要參考迴圈中的元件，請參閱 [ (dotnet/aspnetcore #13358) ，捕捉多個類似子元件的參考 ](https://github.com/dotnet/aspnetcore/issues/13358)。

雖然捕捉元件參考使用類似的語法來 [捕捉元素參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)，但它並不是 JavaScript interop 功能。 元件參考不會傳遞至 JavaScript 程式碼。 元件參考僅適用于 .NET 程式碼。

> [!NOTE]
> 請勿 **使用元件** 參考來改變子元件的狀態。 相反地，請使用一般宣告式參數將資料傳遞給子元件。 使用一般宣告式參數會導致子元件自動 rerender 正確的時間。

## <a name="synchronization-context"></a>同步處理內容

Blazor 使用同步處理內容 (<xref:System.Threading.SynchronizationContext>) 來強制執行單一邏輯執行緒。 元件的 [生命週期方法](xref:blazor/components/lifecycle) 和所引發的任何事件回呼 Blazor 都會在同步處理內容上執行。

Blazor Server的同步處理內容會嘗試模擬單一執行緒環境，使它與瀏覽器中的 WebAssembly 模型緊密相符，也就是單一執行緒。 在任何給定的時間點，工作只會在一個執行緒上執行，以提供單一邏輯執行緒的印象。 不會同時執行兩個作業。

### <a name="avoid-thread-blocking-calls"></a>避免執行緒封鎖呼叫

一般而言，請勿呼叫下列方法。 下列方法會封鎖執行緒，因此會封鎖應用程式繼續工作，直到基礎 <xref:System.Threading.Tasks.Task> 完成為止：

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>在外部叫用元件方法以更新狀態

在事件中，必須根據外來事件（例如計時器或其他通知）更新元件，然後使用 `InvokeAsync` 方法來分派至 Blazor 同步處理內容。 例如，假設有一個可通知任何已更新狀態之接聽元件的通知程式 *服務* ：

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

註冊 `NotifierService` ：

* 在中 Blazor WebAssembly ，以 singleton 的形式註冊服務 `Program.Main` ：

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* 在中 Blazor Server ，註冊服務的範圍如下 `Startup.ConfigureServices` ：

  ```csharp
  services.AddScoped<NotifierService>();
  ```

使用 `NotifierService` 更新元件：

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

在上述範例中：

* `NotifierService` 在的同步處理內容之外叫用元件的 `OnNotify` 方法 Blazor 。 `InvokeAsync` 用來切換至正確的內容，並將轉譯排入佇列。 如需詳細資訊，請參閱<xref:blazor/components/rendering>。
* 元件會執行 <xref:System.IDisposable> ，而且 `OnNotify` 委派會在方法中取消訂閱，而此 `Dispose` 方法會在處置元件時由架構呼叫。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>使用 \@ 金鑰來控制元素和元件的保留

當轉譯元素或元件的清單，以及後續變更的元素或元件時， Blazor 的比較演算法必須決定可以保留先前的元素或元件，以及模型物件應該如何對應至這些專案或元件。 一般來說，此程式是自動的，而且可以忽略，但在某些情況下，您可能會想要控制此進程。

請考慮下列範例：

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

集合的內容 `People` 可能會隨著插入、刪除或重新排序的專案而變更。 元件轉譯中時， `<DetailsEditor>` 元件可能會變更以接收不同的 `Details` 參數值。 這可能會導致比預期更複雜的轉譯資料流程。 在某些情況下，轉譯資料流程可能會導致可見的行為差異，例如遺失的元素焦點。

您可以使用指示詞屬性來控制對應進程 [`@key`][5] 。 [`@key`][5] 使比較演算法保證根據索引鍵的值來保留元素或元件：

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

當 `People` 集合變更時，比較演算法會保留 `<DetailsEditor>` 實例和實例之間的關聯 `person` ：

* 如果 `Person` 從清單中刪除 `People` ，則只 `<DetailsEditor>` 會從 UI 中移除對應的實例。 其他實例則保持不變。
* 如果 `Person` 插入清單中的某個位置，就 `<DetailsEditor>` 會在該對應位置插入一個新的實例。 其他實例則保持不變。
* 如果 `Person` 重新排序專案，則 `<DetailsEditor>` 會保留對應的實例，並在 UI 中重新排序。

在某些情況下，使用可將 [`@key`][5] 轉譯資料流程的複雜性降至最低，並避免變更 DOM 的可設定狀態部分（例如焦點位置）。

> [!IMPORTANT]
> 索引鍵是每個容器元素或元件的本機密鑰。 索引鍵不會在檔之間進行全域比較。

### <a name="when-to-use-key"></a>使用金鑰的時機 \@

一般來說，在轉譯清單時使用 [`@key`][5] (例如，在 [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 區塊) 中，以及定義的適當值 [`@key`][5] 。

當物件變更時，您也可以使用 [`@key`][5] 防止 Blazor 保留元素或元件子樹：

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

如果 `@currentPerson` 有變更，屬性指示詞會 [`@key`][5] 強制 Blazor 捨棄整個 `<div>` 和其子系，並使用新的元素和元件重建 UI 內的子樹。 如果您需要保證在變更時不會保留任何 UI 狀態，這會很有用 `@currentPerson` 。

### <a name="when-not-to-use-key"></a>何時不使用 \@ 金鑰

與進行比較時，會產生效能成本 [`@key`][5] 。 效能成本不大，但只會指定 [`@key`][5] 控制元素或元件保留規則是否能讓應用程式受益。

即使 [`@key`][5] 未使用，也會盡可能 Blazor 保留子項目和元件實例。 使用的唯一優點 [`@key`][5] 是控制模型實例 *如何* 對應至保留的元件實例，而不是選取對應的比較演算法。

### <a name="what-values-to-use-for-key"></a>用於索引鍵的值 \@

一般來說，提供下列其中一種值的意義 [`@key`][5] ：

* 例如， `Person` 如先前範例所示的 (模型物件實例) 。 這可確保根據物件參考相等來保留。
* 唯一識別碼 (例如，、或) 類型的主 `int` 鍵值 `string` `Guid` 。

確定用於的值 [`@key`][5] 不會衝突。 如果在相同的父元素內偵測到衝突值，則會擲回例外狀況， Blazor 因為它無法將舊的元素或元件以決定性的方式對應至新的元素或元件。 只使用相異的值，例如物件實例或主鍵值。

## <a name="overwritten-parameters"></a>覆寫的參數

Blazor架構通常會施加安全的父系對子參數指派：

* 未預期地覆寫參數。
* 最小化副作用。 例如，會避免其他轉譯，因為它們可能會建立無限的轉譯迴圈。

當父元件轉譯中時，子元件會收到可能覆寫現有值的新參數值。 在使用一或多個資料系結參數開發元件時，通常會發生不小心覆寫子元件中的參數值，而開發人員則會直接寫入子系中的參數：

* 子元件會以父元件中的一或多個參數值來呈現。
* 子系直接寫入參數的值。
* 父元件會轉譯中並覆寫子系參數的值。

覆寫辨識值的可能也會延伸至子元件的屬性 setter。

**我們的一般指引是不要建立直接寫入其本身參數的元件。**

請考慮下列有問題的 `Expander` 元件：

* 轉譯子內容。
* 切換 () ，以元件參數顯示子內容 `Expanded` 。
* 元件會直接寫入 `Expanded` 參數，以示範覆寫參數的問題，應該予以避免。

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

`Expander`元件會新增至可能呼叫的父元件 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ：

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

一開始， `Expander` 當元件的屬性切換時，元件會獨立運作 `Expanded` 。 子元件會如預期般維護其狀態。 當 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在父系中呼叫時， `Expanded` 第一個子元件的參數會重設回其初始值 (`true`) 。 第二個 `Expander` 元件的 `Expanded` 值不會重設，因為不會在第二個元件中轉譯任何子內容。

若要在先前的案例中維持狀態，請使用元件中的 *私用欄位* `Expander` 來維持其切換狀態。

下列修訂的 `Expander` 元件：

* 接受 `Expanded` 來自父系的元件參數值。
* 將元件參數值指派給 `expanded` [OnInitialized 事件](xref:blazor/components/lifecycle#component-initialization-methods)中 () 的私用欄位。
* 使用私用欄位來維護其內部切換狀態，以示範如何避免直接寫入參數。

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

如需詳細資訊，請參閱[ Blazor (dotnet/aspnetcore #24599) 的雙向](https://github.com/dotnet/aspnetcore/issues/24599)系結錯誤。 

## <a name="apply-an-attribute"></a>套用屬性

您可以使用指示詞將屬性套用至 Razor 元件 [`@attribute`][7] 。 下列範例會將[ `[Authorize]` 屬性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)套用至元件類別：

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>條件式 HTML 元素屬性

HTML 元素屬性是根據 .NET 值以有條件的形式呈現。 如果值為 `false` 或 `null` ，則不會呈現屬性。 如果值為 `true` ，則會將屬性轉譯為最小化。

在下列範例中， `IsCompleted` 判斷 `checked` 是否在專案的標記中轉譯：

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

如果 `IsCompleted` 為 `true` ，則會將此核取方塊轉譯為：

```html
<input type="checkbox" checked />
```

如果 `IsCompleted` 為 `false` ，則會將此核取方塊轉譯為：

```html
<input type="checkbox" />
```

如需詳細資訊，請參閱[ Razor ASP.NET Core 的語法參考](xref:mvc/views/razor)。

> [!WARNING]
> 當 .NET 類型為時，某些 HTML 屬性（例如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) ）無法正常運作 `bool` 。 在這些情況下，請使用型別， `string` 而不是 `bool` 。

## <a name="raw-html"></a>原始 HTML

字串通常會使用 DOM 文位元組點來呈現，這表示它們所包含的標記會被忽略，並視為常值文字。 若要轉譯原始 HTML，請將 HTML 內容包裝成 `MarkupString` 值。 值會剖析為 HTML 或 SVG 並插入 DOM 中。

> [!WARNING]
> 轉譯從任何未受信任來源所建立的原始 HTML 會有 **安全性風險** ，應予以避免！

下列範例顯示如何使用 `MarkupString` 型別，將靜態 HTML 內容的區塊加入至元件的轉譯輸出：

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Razor 範本

您可以使用範本語法來定義轉譯片段 Razor 。 Razor 範本是定義 UI 程式碼片段的方式，並採用下列格式：

```razor
@<{HTML tag}>...</{HTML tag}>
```

下列範例說明如何 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 在元件中直接指定和值，以及轉譯範本。 轉譯片段也可以做為引數傳遞至樣板 [化元件](xref:blazor/components/templated-components)。

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

上述程式碼的呈現輸出：

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>靜態資產

Blazor遵循 ASP.NET Core 應用程式將靜態資產放置於專案[ `web root (wwwroot)` 資料夾](xref:fundamentals/index#web-root)底下的慣例。

使用基底相對路徑 (`/`) 參考靜態資產的 web 根目錄。 在下列範例中， `logo.png` 實際上是位於資料夾中 `{PROJECT ROOT}/wwwroot/images` ：

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor 元件 () **不** 支援波狀符號斜線標記法 `~/` 。

如需設定應用程式基底路徑的詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。

## <a name="tag-helpers-arent-supported-in-components"></a>元件中不支援標記協助程式

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro))  (的元件中不支援 Razor `.razor` 。 若要在中提供標籤協助程式的功能，請使用與標籤協助程式 Blazor 相同的功能建立元件，並改為使用該元件。

## <a name="scalable-vector-graphics-svg-images"></a>可擴充的向量圖形 (SVG) 影像

由於 Blazor 呈現 HTML、瀏覽器支援的影像，包括可擴充的向量圖形 (SVG) 影像 (`.svg`) ）可透過 `<img>` 標記支援：

```html
<img alt="Example image" src="some-image.svg" />
```

同樣地， () 的樣式表單檔案的 CSS 規則中支援 SVG 影像 `.css` ：

```css
.my-element {
    background-image: url("some-image.svg");
}
```

不過，並非所有案例都支援內嵌 SVG 標記。 如果您將 `<svg>` 標記直接放入元件檔 (`.razor`) ，則支援基本映射轉譯，但目前尚不支援許多先進的案例。 例如， `<use>` 目前未遵守標記，且 [`@bind`][10] 無法與某些 SVG 標記一起使用。 如需詳細資訊，請參閱 [ Blazor (dotnet/aspnetcore 中的 SVG 支援 #18271) ](https://github.com/dotnet/aspnetcore/issues/18271)。

## <a name="whitespace-rendering-behavior"></a>空白轉譯行為

::: moniker range=">= aspnetcore-5.0"

除非使用指示詞搭配的 [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) 值，否則 `true` 如果有下列情況，則預設會移除額外的空格：

* 元素內的前置或尾端。
* 參數內的前置或尾端 `RenderFragment` 。 例如，傳遞至另一個元件的子內容。
* 它在 c # 程式碼區塊之前或之後，例如 `@if` 或 `@foreach` 。

使用 CSS 規則（例如）時，移除空白可能會影響轉譯的輸出 `white-space: pre` 。 若要停用此效能優化並保留空白字元，請採取下列其中一項動作：

* 將指示詞新增至檔案 `@preservewhitespace true` 頂端 `.razor` ，以將喜好設定套用至特定元件。
* 在檔案內加入指示詞 `@preservewhitespace true` `_Imports.razor` ，以將喜好設定套用至整個子目錄或整個專案。

在大部分情況下，不需要採取任何動作，因為應用程式通常會繼續正常運作 (但) 更快。 如果移除空白字元會造成特定元件的任何問題，請 `@preservewhitespace true` 在該元件中使用，以停用這項優化。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

空白會保留在元件的原始程式碼中。 即使沒有視覺效果，也會在瀏覽器的檔物件模型 (DOM) 中呈現僅限空白字元的文字。

請考慮下列 Razor 元件程式碼：

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

上述範例會轉譯下列不必要的空格：

* 在程式 `@foreach` 代碼區塊之外。
* 在 `<li>` 元素周圍。
* 在 `@item.Text` 輸出周圍。

包含100專案的清單會導致402個空白區域，而且沒有任何額外的空白字元會以視覺化方式影響轉譯的輸出。

為元件呈現靜態 HTML 時，不會保留標記內的空白字元。 例如，在轉譯的輸出中查看下列元件的來源：

```razor
<img     alt="My image"   src="img.png"     />
```

空白字元不會保留在上述 Razor 標記中：

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:blazor/security/server/threat-mitigation>：包含建立 Blazor Server 必須與資源耗盡相關之應用程式的指引。

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[2]: <xref:mvc/views/razor#using> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[3]: <xref:mvc/views/razor#attributes> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[4]: <xref:mvc/views/razor#ref> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[5]: <xref:mvc/views/razor#key> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[6]: <xref:mvc/views/razor#inherits> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[7]: <xref:mvc/views/razor#attribute> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[8]: <xref:mvc/views/razor#namespace> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[9]: <xref:mvc/views/razor#page> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
[10]: <xref:mvc/views/razor#bind> "：：：非 loc (Razor) ：：： ASP.NET Core 的語法參考"
