---
title: ASP.NET Core Blazor WebAssembly 效能最佳做法
author: pranavkm
description: 提升 ASP.NET Core Blazor WebAssembly 應用程式效能，並避免常見效能問題的秘訣。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 0753ef0f1cde7bbb45ecc09b97fecb5ce364811c
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024648"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly 效能最佳做法

[Pranav Krishnamoorthy](https://github.com/pranavkm)和[Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly 經過仔細設計和優化，可在最實際的應用程式 UI 案例中啟用高效能。 不過，產生最佳結果取決於使用正確模式和功能的開發人員。 請考慮下列層面：

* **執行時間輸送量**： .net 程式碼會在 WebAssembly 執行時間內的解譯器上執行，因此 CPU 輸送量受限。 在要求的案例中，應用程式受益于 [優化轉譯速度](#optimize-rendering-speed)。
* **啟動時間**：應用程式將 .net 執行時間傳送到瀏覽器，因此請務必使用 [最小化應用程式下載大小](#minimize-app-download-size)的功能。

## <a name="optimize-rendering-speed"></a>優化轉譯速度

下列各節提供建議來將轉譯工作負載降到最低，並改善 UI 回應性。 遵循這項建議，可輕鬆地在 UI 轉譯速度中進行 *十折迭或更高的改進* 。

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>避免不必要的元件子樹呈現

在執行時間，元件會以階層形式存在。 根元件具有子元件。 接著，根的子系有自己的子元件，依此類推。 當事件發生時（例如使用者選取按鈕），這會 Blazor 決定要 rerender 的元件：

1. 事件本身會分派到轉譯事件處理常式的任何元件。 執行事件處理常式之後，該元件就會保存。
1. 每當保存任何元件時，它就會將參數值的新複本提供給其子元件。
1. 當接收一組新的參數值時，每個元件都會選擇是否要 rerender。 根據預設，元件 rerender 如果參數值可能已變更 (例如，如果它們是可變動的物件) 。

此序列的最後兩個步驟會以遞迴方式從元件階層中繼續進行。 在許多情況下，會保存整個子樹。 這表示以高階元件為目標的事件可能會造成昂貴的轉譯資料流程程式，因為該點下的所有內容都必須保存。

如果您想要中斷此程式，並防止將遞迴轉譯成特定的子樹狀結構，您可以執行下列其中一項：

* 確定特定元件的所有參數都是基本的不可變類型 (例如、、 `string` 、 `int` `bool` `DateTime` 和其他) 。 如果未變更這些參數值，則偵測變更的內建邏輯會自動略過轉譯資料流程。 如果您以轉譯子元件 `<Customer CustomerId="@item.CustomerId" />` ，其中 `CustomerId` 是一個 `int` 值，則除非變更，否則不會保存 `item.CustomerId` 。
* 如果您需要接受 nonprimitive 參數值（例如自訂模型類型、事件回呼或 <xref:Microsoft.AspNetCore.Components.RenderFragment> 值），則可以覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 來控制是否要轉譯的決策，這在[ `ShouldRender` 使用](#use-of-shouldrender)一節中有說明。

藉由略過整個子樹的轉譯資料流程，您可以在事件發生時移除大部分的轉譯成本。

您可能想要特別考慮子元件，讓您可以略過該部分 UI 的轉譯資料流程。 這是減少父元件之轉譯成本的有效方式。

#### <a name="use-of-shouldrender"></a>使用 `ShouldRender`

如果撰寫的僅限 UI 元件永遠不會在初始轉譯之後變更 (不論是否有任何參數值) ，請將設定 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 為 return `false` ：

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

如果元件的參數值以特定方式改變時，只需要轉譯資料流程，您就可以使用私用欄位來追蹤所需的資訊，以偵測變更。 在下列範例中， `shouldRender` 是以檢查任何種類的變更或變動來檢查是否會提示 rerender。 `prevOutboundFlightId` 和 `prevInboundFlightId` 追蹤下一次可能更新的資訊：

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

在上述程式碼中，事件處理常式也可以設定 `shouldRender` 為， `true` 以便在事件之後保存元件。

大部分的元件都不需要這種手動控制層級。 如果這些子樹在轉譯時特別耗費資源，而且會造成 UI 延隔，您應該只在意略過轉譯子樹。

如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>虛擬化

在迴圈內轉譯大量的 UI （例如具有上千個專案的清單或方格）時，大量的轉譯作業可能會導致 UI 轉譯延遲，因而導致使用者體驗不佳。 假設使用者一次只能看到少量的元素，而不需要滾動，那麼花太多時間轉譯專案目前看起來很浪費。

若要解決這個問題，請 Blazor 提供 `Virtualize` 元件來建立任意大型清單的外觀和滾動行為，但只會轉譯目前滾動塊區內的清單專案。 例如，這表示應用程式可以有一份包含100000個專案的清單，但只需支付20個專案的轉譯成本。 使用 `Virtualize` 元件可以依大小的順序擴大 UI 效能。

如需詳細資訊，請參閱<xref:blazor/components/virtualization>。

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>建立輕量、優化的元件

大部分 Blazor 的元件都不需要積極的優化工作。 這是因為大部分的元件通常不會在 UI 中重複，且不會以高頻率 rerender。 例如， `@page` 代表高階 UI 元件（例如對話或表單）的元件和元件，最有可能只會出現一次，而且只會 rerender 以回應使用者手勢。 這些元件不會建立高轉譯工作負載，因此您可以自由地使用任何您想要的架構功能組合，而不需要擔心轉譯效能。

不過，在某些情況下，您也會建立需要大規模重複的元件。 例如：

* 大型的嵌套表單可能會有數百個個別的輸入、標籤和其他元素。
* 方格可能有數千個數據格。
* 散佈圖可能有數百萬個資料點。

如果以個別的元件實例為每個單位進行模型化，則它們的轉譯效能會很重要。 本節提供讓這類元件變得輕量的建議，讓 UI 保持快速且迅速回應。

#### <a name="avoid-thousands-of-component-instances"></a>避免數以千計的元件實例

每個元件都是個別的島，可獨立于其父代和子系之外轉譯。 藉由選擇如何將 UI 分割為元件的階層，您可以控制 UI 轉譯的資料細微性。 這可能是良好或不良的效能。

* 藉由將 UI 分割成更多元件，當事件發生時，您可以在 UI rerender 中有較小的部分。 例如，當使用者按一下資料表資料列中的按鈕時，您可能只會有該單一資料列 rerender，而不是整個頁面或資料表。
* 不過，每個額外的元件都牽涉到額外的記憶體和 CPU 額外負荷，以處理其獨立的狀態和轉譯生命週期。

在調整 .NET 5 的效能時 Blazor WebAssembly ，我們測量出每個元件實例大約0.06 毫秒的轉譯負擔。 這是以簡單的元件為基礎，接受一般膝上型電腦上執行的三個參數。 就內部而言，此額外負荷主要是因為從字典中取出每個元件的狀態，以及傳遞和接收參數。 藉由乘法，您可以看到加入2000額外元件實例會在轉譯時間增加0.12 秒，而 UI 會開始感覺到使用者的速度變慢。

您可以讓元件更輕量，讓您可以有更多的元件，但通常更強大的技巧就是不會有這麼多的元件。 下列各節描述兩種方法。

##### <a name="inline-child-components-into-their-parents"></a>內嵌子元件至其父代

請考慮下列可呈現子元件序列的元件：

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

在上述的範例程式碼 `<ChatMessageDisplay>` 中，元件定義于 `ChatMessageDisplay.razor` 包含下列內容的檔案中：

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

上述範例會正常運作，而且只要上千個訊息不會顯示一次，就能順利執行。 若要一次顯示數以千計的訊息，請考慮 *不要* 分解個別的 `ChatMessageDisplay` 元件。 相反地，直接將轉譯內嵌至父系：

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

這可避免轉譯的每個元件的額外負荷，因為無法個別 rerender 每個元件的成本。

##### <a name="define-reusable-renderfragments-in-code"></a>在程式碼中定義可重複使用 `RenderFragments`

您可以單純地將子元件分解為重複使用轉譯邏輯的方法。 如果是這種情況，您仍然可以重複使用轉譯邏輯，而不需要宣告實際元件。 在任何元件的 `@code` 區塊中，您可以定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> 發出 UI 的，並可從任何位置呼叫：

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

如同上述範例中的 demonstated，元件可以從區塊內的程式碼 `@code` 和外部發出標記。 這種方法會定義一個委派，可 <xref:Microsoft.AspNetCore.Components.RenderFragment> 讓您在元件的正常轉譯輸出內轉譯（選擇性地在多個位置）。 委派必須接受型別為的參數， `__builder` <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以便 Razor 編譯器可以產生其轉譯指令。

如果您想要在多個元件之間重複使用，請考慮將它宣告為 `public static` 成員：

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

這現在可以從不相關的元件叫用。 這項技術適用于建立可重複使用之標記程式碼片段的程式庫，而不需要每個元件的額外負荷。

<xref:Microsoft.AspNetCore.Components.RenderFragment> 委派也可以接受參數。 若要建立 `ChatMessageDisplay` 先前範例中元件的對等專案：

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

這種方法可讓您在不需要每個元件的額外負荷下重複使用轉譯邏輯。 不過，它無法獨立重新整理其 UI 的子樹，也無法在其父代呈現時略過呈現 UI 的子樹，因為沒有元件界限。

#### <a name="dont-receive-too-many-parameters"></a>不要收到太多參數

如果元件經常重複（例如數百次或數千次），請記住，傳遞和接收每個參數的額外負荷。

太多參數很少會嚴重限制效能，但它可能是一個因素。 針對在 `<TableCell>` 方格內轉譯1000時間的元件，傳遞給它的每個額外參數可能會在總轉譯成本周圍加上15毫秒。 如果每個資料格都接受10個參數，則每個元件轉譯的參數傳遞大約是150毫秒，因此可能是150000毫秒 (150 秒) ，而且本身會造成遲鈍的 UI。

若要減少這項負載，您可以透過自訂類別組合多個參數。 例如， `<TableCell>` 元件可能會接受：

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

在上述範例中， `Data` 每個資料格都是不同的，但在所有資料格 `Options` 之間是共通的。 當然，這可能是因為不具有 `<TableCell>` 元件，而是將其邏輯內嵌至父元件的改進。

#### <a name="ensure-cascading-parameters-are-fixed"></a>確定已修正串聯參數

`<CascadingValue>`元件有一個稱為的選擇性參數 `IsFixed` 。

* 如果 `IsFixed` 值 `false` (預設的) ，則串聯值的每個收件者都會設定要接收變更通知的訂用帳戶。 在這種情況下，因為訂用帳戶追蹤的緣故，每個服務的 `[CascadingParameter]` **成本都相當高** `[Parameter]` 。
* 如果 `IsFixed` 值是 `true` (例如 `<CascadingValue Value="@someValue" IsFixed="true">`) ，則收件者會收到初始值，但 *不* 會設定任何訂用帳戶來接收更新。 在此情況下，每個 `[CascadingParameter]` 都是輕量的，而且不會比一般標準 **更昂貴** `[Parameter]` 。

因此，您應該盡可能 `IsFixed="true"` 在串聯的值上使用。 只要所提供的值不會隨著時間而變更，您就可以這麼做。 在元件以串聯值形式傳遞的一般模式 `this` 下，您應該使用 `IsFixed="true"` ：

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

如果有大量的其他元件會收到串聯的值，這就會有很大的差異。 如需詳細資訊，請參閱<xref:blazor/components/cascading-values-and-parameters>。

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>避免使用屬性展開 `CaptureUnmatchedValues`

元件可以選擇使用旗標接收「不相符的」參數值 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> ：

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

這種方法可讓您將任意額外的屬性傳遞至元素。 不過，它也相當昂貴，因為轉譯器必須：

* 將所有提供的參數與已知參數的集合進行比對，以建立字典。
* 追蹤相同屬性的多個複本如何覆寫彼此。

您可以隨意使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 非效能關鍵的元件，例如不會經常重複的元件。 但是針對大規模轉譯的元件（例如大型清單中的每個專案或方格中的資料格），請嘗試避免屬性展開。

如需詳細資訊，請參閱<xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>。

#### <a name="implement-setparametersasync-manually"></a>`SetParametersAsync`手動執行

每一元件轉譯負荷的主要部分是將傳入的參數值寫入 `[Parameter]` 屬性。 轉譯器必須使用反映來執行此作業。 雖然這是經過優化的，但在 WebAssembly 執行時間上缺少 JIT 支援會造成限制。

在某些極端的情況下，您可能會想要避免反映並手動執行您自己的參數設定邏輯。 這可能適用于：

* 您的元件通常會轉譯 (例如，UI) 中有數百個或數千個複本。
* 它接受許多參數。
* 您發現接收參數的額外負荷會對 UI 回應性產生明顯的影響。

在這些情況下，您可以覆寫元件的虛擬 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，並執行您自己的元件特定邏輯。 下列範例故意避免任何字典查閱：

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

在上述程式碼中，傳回基類 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 會執行一般生命週期方法，而不會再次指派參數。

如您在上述程式碼中所見，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 和提供自訂邏輯既複雜又繁瑣，因此我們通常不建議採用這種方法。 在極端情況下，它可以改善20-25% 的轉譯效能，但您應該只在先前所列的案例中考慮這種方法。

### <a name="dont-trigger-events-too-rapidly"></a>不要太快觸發事件

有些瀏覽器事件非常頻繁地引發，例如 `onmousemove` 和 `onscroll` ，可能會每秒引發數十或數百次。 在大多數情況下，您不需要經常執行 UI 更新。 如果您嘗試這麼做，可能會傷害 UI 回應性或耗用過多的 CPU 時間。

`@onmousemove` `@onscroll` 您可能會想要使用 JS interop 來註冊較不常引發的回呼，而不是使用原生或事件。 例如，下列元件 () 會 `MyComponent.razor` 顯示滑鼠的位置，但每隔500毫秒最多隻會更新一次：

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

對應的 JavaScript 程式碼（可以放在頁面中， `index.html` 或以 ES6 模組的形式載入）會註冊實際的 DOM 事件接聽程式。 在此範例中，事件接聽程式會使用 [lodash 所的 `throttle` 函數](https://lodash.com/docs/4.17.15#throttle) 來限制調用的速率：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

這項技術對於而言更為重要 Blazor Server ，因為每個事件調用都牽涉到透過網路傳遞訊息。 這是很重要的， Blazor WebAssembly 因為它可以大幅減少轉譯工作的數量。

## <a name="optimize-javascript-interop-speed"></a>優化 JavaScript interop 速度

.NET 和 JavaScript 之間的呼叫牽涉到一些額外的額外負荷，因為：

* 依預設，呼叫是非同步。
* 根據預設，參數和傳回值為 JSON 序列化。 這是為了在 .NET 與 JavaScript 類型之間提供容易瞭解的轉換機制。

此外 Blazor Server ，這些呼叫會跨網路傳遞。

### <a name="avoid-excessively-fine-grained-calls"></a>避免過度精細的呼叫

因為每個呼叫都牽涉到一些額外負荷，所以減少呼叫次數可能很有價值。 請考慮下列程式碼，它會在瀏覽器的存放區中儲存專案的集合 `localStorage` ：

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

上述範例會針對每個專案進行個別的 JS interop 呼叫。 相反地，下列方法會將 JS interop 縮減為單一呼叫：

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

對應的 JavaScript 函式定義如下：

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

對於 Blazor WebAssembly ，這通常只有在您正在進行大量 JS interop 呼叫時才會有問題。

### <a name="consider-making-synchronous-calls"></a>請考慮進行同步呼叫

根據預設，JavaScript interop 呼叫是非同步，不論呼叫的程式碼是同步或非同步。 這是為了確保元件與和都相容 Blazor WebAssembly Blazor Server 。 在上 Blazor Server ，所有 JavaScript interop 呼叫都必須是非同步，因為它們是透過網路連接來傳送。

如果您知道您的應用程式只會在上執行 Blazor WebAssembly ，您可以選擇進行同步的 JavaScript interop 呼叫。 這比進行非同步呼叫的額外負荷稍微低，而且可能會產生較少的轉譯迴圈，因為等待結果時沒有中繼狀態。

若要從 .NET 進行同步呼叫至 JavaScript，請轉換 <xref:Microsoft.JSInterop.IJSRuntime> 成 <xref:Microsoft.JSInterop.IJSInProcessRuntime> ：

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

使用時 `IJSObjectReference` ，您可以藉由轉換成來進行同步呼叫 `IJSInProcessObjectReference` 。

::: moniker-end

若要從 JavaScript 對 .NET 進行同步呼叫，請使用 `DotNet.invokeMethod` 而不是 `DotNet.invokeMethodAsync` 。

同步呼叫會在下列情況中運作：

* 應用程式正在執行 Blazor WebAssembly ，而不是 Blazor Server 。
* 呼叫的函式會以同步方式傳回值 (它不是 `async` 方法，也不會傳回 .net <xref:System.Threading.Tasks.Task> 或 JavaScript `Promise`) 。

如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet>。

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>考慮進行 unmarshalled 呼叫

在上執行時，您可以 Blazor WebAssembly 從 .net 對 JavaScript 進行 unmarshalled 呼叫。 這些是未執行引數或傳回值之 JSON 序列化的同步呼叫。 .NET 和 JavaScript 標記法中的記憶體管理和翻譯的所有層面，都是由開發人員所組成。

> [!WARNING]
> 雖然使用 `IJSUnmarshalledRuntime` 具有最少的 JS interop 方法額外負荷，但與這些 api 互動所需的 JavaScript api 目前尚未記載，並且在未來版本中可能會有重大變更。

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>最小化應用程式下載大小

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>中繼語言 (IL) 修剪

[從 Blazor WebAssembly 應用程式修剪未使用的元件](xref:blazor/host-and-deploy/configure-trimmer) ，會移除應用程式二進位檔中未使用的程式碼，以減少應用程式的大小。 預設會在發行應用程式時執行修剪器。 若要從修剪中獲益，請使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令並將 [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) 選項設為，以發佈應用程式進行部署 `Release` ：

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a> (IL) 連結的中繼語言

[連結 Blazor WebAssembly 應用程式](xref:blazor/host-and-deploy/configure-linker) 會藉由在應用程式的二進位檔中修剪未使用的程式碼，來減少應用程式的大小。 依預設，只有在設定中建立時，才會啟用中繼語言 (IL) 連結器 `Release` 。 若要從中獲益，請使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令並將 [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) 選項設為，以發行應用程式進行部署 `Release` ：

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>使用 System.Text.Js于

Blazor的 JS interop 實行相依于 <xref:System.Text.Json> ，這是具有低記憶體配置的高效能 JSON 序列化程式庫。 使用 <xref:System.Text.Json> 不會導致額外的應用程式承載大小超過新增一或多個替代的 JSON 程式庫。

如需遷移指引，請參閱[如何從 `Newtonsoft.Json` 遷移 `System.Text.Json` 至](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。

### <a name="lazy-load-assemblies"></a>延遲載入元件

當路由需要元件時，于執行時間載入元件。 如需詳細資訊，請參閱<xref:blazor/webassembly-lazy-load-assemblies>。

### <a name="compression"></a>壓縮

Blazor WebAssembly發佈應用程式時，會在發行期間以靜態方式壓縮輸出，以減少應用程式的大小，並移除執行時間壓縮的額外負荷。 Blazor 依賴伺服器來執行內容 negotation，並提供靜態壓縮檔案。

部署應用程式之後，請確認應用程式會提供壓縮檔案。 檢查瀏覽器開發人員工具中的 [網路] 索引標籤，並確認檔案是由或提供服務 `Content-Encoding: br` `Content-Encoding: gz` 。 如果主機未提供壓縮檔案，請依照中的指示進行 <xref:blazor/host-and-deploy/webassembly#compression> 。

### <a name="disable-unused-features"></a>停用未使用的功能

Blazor WebAssembly的執行時間包含下列 .NET 功能，如果應用程式不需要它們來取得較小的承載大小，則可以停用這些功能：

* 包含資料檔案，以使時區資訊正確無誤。 如果應用程式不需要這項功能，請考慮將 `BlazorEnableTimeZoneSupport` 應用程式專案檔中的 MSBuild 屬性設為，以停用它 `false` ：

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* 依預設，會 Blazor WebAssembly 在使用者的文化特性中攜帶顯示值所需的全球化資源，例如日期和貨幣。 如果應用程式不需要當地語系化，您可以 [設定應用程式以支援以](xref:blazor/globalization-localization)文化特性為基礎的非變異文化特性 `en-US` ：

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* 包含了定序資訊，以使 Api <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 正常運作。 如果您確定應用程式不需要定序資料，請考慮將 `BlazorWebAssemblyPreserveCollationData` 應用程式專案檔中的 MSBuild 屬性設為，以停用它 `false` ：

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
