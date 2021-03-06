---
title: 處理 ASP.NET Core 應用程式中的錯誤 Blazor
author: guardrex
description: 探索 ASP.NET Core 如何 Blazor Blazor 管理未處理的例外狀況，以及如何開發偵測和處理錯誤的應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395093"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>處理 ASP.NET Core 應用程式中的錯誤 Blazor

本文說明如何 Blazor 管理未處理的例外狀況，以及如何開發偵測和處理錯誤的應用程式。

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a>開發期間的詳細錯誤

當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。 發生錯誤時， Blazor 應用程式會在畫面底部顯示淺黃色的橫條：

* 在開發期間，橫條會將您導向瀏覽器主控台，您可以在其中查看例外狀況。
* 在生產環境中，橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。

此錯誤處理體驗的 UI 是[ Blazor 專案範本](xref:blazor/project-structure)的一部分。

在 Blazor WebAssembly 應用程式中，自訂檔案的體驗 `wwwroot/index.html` ：

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui`元素通常是隱藏的，因為 `display: none` `blazor-error-ui` 應用程式樣式表單中 CSS 類別的樣式存在， (`wwwroot/css/app.css`) 。 當發生錯誤時，架構會套用 `display: block` 至元素。

## <a name="manage-unhandled-exceptions-in-developer-code"></a>管理開發人員程式碼中未處理的例外狀況

若要讓應用程式在發生錯誤之後繼續，應用程式必須有錯誤處理邏輯。 本文稍後的章節將說明可能的未處理例外狀況來源。

在生產環境中，請勿在 UI 中呈現架構例外狀況訊息或堆疊追蹤。 轉譯例外狀況訊息或堆疊追蹤可能：

* 將機密資訊洩漏給終端使用者。
* 協助惡意使用者探索應用程式中可能會危害應用程式、伺服器或網路安全性的弱點。

## <a name="global-exception-handling"></a>全域例外狀況處理

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a>使用持續性提供者記錄錯誤

如果發生未處理的例外狀況，則會將例外狀況記錄到 <xref:Microsoft.Extensions.Logging.ILogger> 服務容器中設定的實例。 根據預設， Blazor 應用程式會使用主控台記錄提供者來記錄主控台輸出。 請考慮將錯誤資訊傳送至後端 web API，以使用記錄提供者搭配記錄檔大小管理和記錄檔輪替，以記錄到伺服器上的永久位置。 此外，後端 web API 應用程式可以使用應用程式效能管理 (APM) 服務（例如[Azure Application Insights (Azure 監視器) &dagger; ](/azure/azure-monitor/app/app-insights-overview)）記錄從用戶端接收的錯誤資訊。

您必須決定要記錄哪些事件，以及記錄事件的嚴重性層級。 惡意使用者可能可以刻意觸發錯誤。 例如，請勿從 `ProductId` 顯示產品詳細資料的元件 URL 中提供未知的錯誤記錄事件。 並非所有錯誤都應該視為記錄的事件。

如需詳細資訊，請參閱下列文章：

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

&dagger;支援應用程式的原生 [Application Insights](/azure/azure-monitor/app/app-insights-overview) 功能 Blazor WebAssembly 和 Blazor [Google Analytics](https://analytics.google.com/analytics/web/) 的原生架構支援可能會在這些技術的未來版本中提供。 如需詳細資訊，請參閱 [在 WASM 用戶端中支援 App Insights Blazor (Microsoft/ApplicationInsights-dotnet #2143) ](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) 和 [Web 分析和診斷 (包含)  (dotnet/aspnetcore #5461 ](https://github.com/dotnet/aspnetcore/issues/5461)) 的連結。 在此同時，用戶端應用程式 Blazor WebAssembly 可以使用具有[JS Interop](xref:blazor/call-javascript-from-dotnet)的[application insights JavaScript SDK](/azure/azure-monitor/app/javascript) ，直接將錯誤記錄到來自用戶端應用程式的 application insights。

&Dagger;適用于應用程式的 web API 後端應用程式的伺服器端 ASP.NET Core 應用程式 Blazor 。 用戶端應用程式會將錯誤資訊設陷並傳送至 web API，而該 API 會將錯誤資訊記錄至持續性記錄提供者。

## <a name="places-where-errors-may-occur"></a>可能發生錯誤的位置

架構和應用程式程式碼可能會在下列任何位置中觸發未處理的例外狀況，這會在本文的下列各節中進一步說明：

* [元件具現化](#component-instantiation-webassembly)
* [生命週期方法](#lifecycle-methods-webassembly)
* [轉譯邏輯](#rendering-logic-webassembly)
* [事件處理常式](#event-handlers-webassembly)
* [元件處置](#component-disposal-webassembly)
* [JavaScript Interop](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly">元件具現化</h3>

當 Blazor 建立元件的實例時：

* 會叫用元件的函式。
* 系統會叫用透過指示詞 [`@inject`](xref:mvc/views/razor#inject) 或[ `[Inject]` 屬性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供給元件之函式之任何非 singleton DI 服務的函式。

在執行的函式或任何屬性的 setter 中發生錯誤 `[Inject]` ，會導致未處理的例外狀況，並阻止架構將元件具現化。 如果函式邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。

<h3 id="lifecycle-methods-webassembly">生命週期方法</h3>

在元件的存留期內，會叫用 Blazor [生命週期方法](xref:blazor/components/lifecycle#lifecycle-methods)。 若要讓元件處理生命週期方法中的錯誤，請新增錯誤處理邏輯。

在下列範例中，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 呼叫方法來取得產品：

* 方法中擲回的例外狀況 `ProductRepository.GetProductByIdAsync` 是由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句處理。
* `catch`執行區塊時：
  * `loadFailed` 設定為 `true` ，用來向使用者顯示錯誤訊息。
  * 會記錄錯誤。

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly">轉譯邏輯</h3>

元件檔 () 中的宣告式標記 Razor `.razor` 會編譯成名為的 c # 方法 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 。 當元件呈現時，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 執行並建立描述所轉譯元件之元素、文字和子元件的資料結構。

轉譯邏輯可能會擲回例外狀況。 當評估但為時，就會發生這種情況的範例 `@someObject.PropertyName` `@someObject` `null` 。

若要避免轉譯 <xref:System.NullReferenceException> 邏輯中的，請 `null` 先檢查物件，再存取其成員。 在下列範例中， `person.Address` 如果是，則不會存取屬性 `person.Address` `null` ：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

上述程式碼假設 `person` 不是 `null` 。 程式碼的結構通常會保證物件存在於轉譯元件時。 在這些情況下，不需要檢查轉譯 `null` 邏輯。 在先前的範例中， `person` 可能保證存在，因為 `person` 會在元件具現化時建立，如下列範例所示：

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly">事件處理常式</h3>

使用下列專案建立事件處理常式時，用戶端程式代碼會觸發 c # 程式碼的調用：

* `@onclick`
* `@onchange`
* 其他 `@on...` 屬性
* `@bind`

在這些案例中，事件處理常式程式碼可能會擲回未處理的例外狀況。

如果應用程式呼叫可能因為外部原因而失敗的程式碼，請使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。

如果使用者程式碼未攔截並處理例外狀況，則架構會記錄例外狀況。

<h3 id="component-disposal-webassembly">元件處置</h3>

您可以從 UI 中移除元件，例如，因為使用者已流覽至另一個頁面。 從 UI 中移除實作為的元件時 <xref:System.IDisposable?displayProperty=fullName> ，架構會呼叫元件的 <xref:System.IDisposable.Dispose%2A> 方法。

如果處置邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。

如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。

<h3 id="javascript-interop-webassembly">JavaScript Interop</h3>

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允許 .NET 程式碼在使用者的瀏覽器中對 JavaScript 執行時間進行非同步呼叫。

下列條件適用于的錯誤處理 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> ：

* 如果同步的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 失敗，就會發生 .net 例外狀況。 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為無法序列化提供的引數。 開發人員程式碼必須攔截例外狀況。
* 如果 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 非同步呼叫失敗，則 .net <xref:System.Threading.Tasks.Task> 會失敗。 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為 JavaScript 端程式碼會擲回例外狀況，或傳回 `Promise` 做為完成的 `rejected` 。 開發人員程式碼必須攔截例外狀況。 如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 運算子，請考慮使用錯誤處理和記錄，將方法呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中。
* 依預設，對的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 必須在特定期間內完成，否則會呼叫超時。預設的超時時間是一分鐘。 此超時會防止程式碼遺失網路連線或永遠不會傳回完成訊息的 JavaScript 程式碼。 如果呼叫超時，則產生的 <xref:System.Threading.Tasks> 會失敗，並出現 <xref:System.OperationCanceledException> 。 使用記錄來攔截和處理例外狀況。

同樣地，JavaScript 程式碼可能會起始對[ `[JSInvokable]` 屬性](xref:blazor/call-dotnet-from-javascript)所表示之 .net 方法的呼叫。 如果這些 .NET 方法擲回未處理的例外狀況，則 `Promise` 會拒絕 JavaScript 端。

您可以選擇在 .NET 端或方法呼叫的 JavaScript 端使用錯誤處理常式代碼。

如需詳細資訊，請參閱下列文章：

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a>進階案例

### <a name="recursive-rendering"></a>遞迴轉譯

元件可以用遞迴方式進行嵌套。 這適用于表示遞迴資料結構。 例如， `TreeNode` 元件可以 `TreeNode` 針對每個節點的子系呈現更多元件。

以遞迴方式呈現時，請避免產生無限遞迴的程式碼模式：

* 不要以遞迴方式呈現包含迴圈的資料結構。 例如，不要轉譯其子系本身包含的樹狀節點。
* 請勿建立包含迴圈的版面配置鏈。 例如，請勿建立版面配置本身的配置。
* 不允許終端使用者違反遞迴不變數 (規則) 透過惡意資料輸入或 JavaScript interop 呼叫。

轉譯期間的無限迴圈：

* 導致轉譯程式永遠繼續。
* 相當於建立未結束的迴圈。

在這些情況下，執行緒通常會嘗試：

* 無限期耗用作業系統所允許的 CPU 時間。
* 耗用無限數量的用戶端記憶體。 使用無限制的記憶體相當於未結束的迴圈在每次反復專案中將專案新增至集合的案例。

若要避免無限遞迴模式，請確定遞迴轉譯程式碼包含適當的停止條件。

### <a name="custom-render-tree-logic"></a>自訂呈現樹狀結構邏輯

大部分的 Blazor 元件會實作為 Razor 元件檔 (`.razor`) ，並由架構編譯以產生可在上操作的邏輯， <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以呈現其輸出。 不過，開發人員可以使用程式化 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> c # 程式碼手動執行邏輯。 如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。

> [!WARNING]
> 手動轉譯樹狀結構產生器邏輯的使用會被視為 advanced 和 unsafe 案例，不建議用於一般元件開發。

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>撰寫程式碼時，開發人員必須保證程式碼的正確性。 例如，開發人員必須確保：

* 對 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的呼叫會正確平衡。
* 只會將屬性新增至正確的位置。

不正確的手動轉譯樹狀 builder 邏輯可能會造成任意未定義的行為，包括損毀、應用程式停止回應和安全性弱點。

請考慮以相同的複雜度層級手動呈現樹狀檢視器邏輯，並以與撰寫元件程式碼或 [Microsoft 中繼語言 (MSIL](/dotnet/standard/managed-code)相同的 *危險* 層級，手動) 的指示。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

&dagger;適用于用戶端應用程式用於記錄的後端 ASP.NET Core web API 應用程式 Blazor WebAssembly 。

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a>開發期間的詳細錯誤

當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。 發生錯誤時， Blazor 應用程式會在畫面底部顯示淺黃色的橫條：

* 在開發期間，橫條會將您導向瀏覽器主控台，您可以在其中查看例外狀況。
* 在生產環境中，橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。

此錯誤處理體驗的 UI 是[ Blazor 專案範本](xref:blazor/project-structure)的一部分。

在 Blazor Server 應用程式中，自訂檔案的體驗 `Pages/_Host.cshtml` ：

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

專案 `blazor-error-ui` 通常是隱藏的，因為 `display: none` `blazor-error-ui` 網站的樣式表單中 CSS 類別的樣式存在， (`wwwroot/css/site.css`) 。 當發生錯誤時，架構會套用 `display: block` 至元素。

## <a name="blazor-server-detailed-circuit-errors"></a>Blazor Server 詳細線路錯誤

用戶端錯誤不包含呼叫堆疊，也不會提供錯誤原因的詳細資料，但伺服器記錄檔會包含這類資訊。 基於開發目的，可透過啟用詳細錯誤，讓用戶端可以使用敏感性電路錯誤資訊。

將 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> 設定為 `true`。 如需詳細資訊和範例，請參閱 <xref:blazor/fundamentals/signalr#circuit-handler-options>。

設定的替代 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> `DetailedErrors` `true` 方式是在應用程式的開發環境設定檔中，將設定金鑰設定為 (`appsettings.Development.json`) 。  此外，設定[ SignalR 伺服器端記錄](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) ，以進行詳細記錄的[Debug](xref:Microsoft.Extensions.Logging.LogLevel)或[Trace](xref:Microsoft.Extensions.Logging.LogLevel) SignalR 。

`appsettings.Development.json`:

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors>設定金鑰也可以設定為 `true` 使用 `ASPNETCORE_DETAILEDERRORS` 環境變數，其值為 `true` 在開發/預備環境伺服器上或在您的本機系統上。

> [!WARNING]
> 一律避免將錯誤資訊公開給網際網路上的用戶端，這會造成安全性風險。

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Blazor Server應用程式如何回應未處理的例外狀況

Blazor Server 是具狀態的架構。 當使用者與應用程式互動時，它們會維持與伺服器的連線，稱為 *線路*。 線路會保存作用中的元件實例，再加上許多其他狀態的層面，例如：

* 元件的最新轉譯輸出。
* 可由用戶端事件觸發的目前事件處理委派集。

如果使用者在多個瀏覽器索引標籤中開啟應用程式，使用者會建立多個獨立線路。

Blazor 將大部分未處理的例外狀況視為嚴重的重大例外狀況。 如果線路因為未處理的例外狀況而終止，則使用者只能透過重載頁面來建立新的線路，以繼續與應用程式互動。 終止的線路以外的線路（其他使用者或其他瀏覽器索引標籤的線路）不受影響。 此案例類似于損毀的桌面應用程式。 損毀的應用程式必須重新開機，但不會影響其他應用程式。

當發生未處理的例外狀況時，架構會終止電路，原因如下：

* 未處理的例外狀況通常會讓電路保持未定義狀態。
* 無法在未處理的例外狀況之後保證應用程式的正常操作。
* 如果線路繼續處於未定義狀態，則應用程式中可能會出現安全性弱點。

## <a name="manage-unhandled-exceptions-in-developer-code"></a>管理開發人員程式碼中未處理的例外狀況

若要讓應用程式在發生錯誤之後繼續，應用程式必須有錯誤處理邏輯。 本文稍後的章節將說明可能的未處理例外狀況來源。

在生產環境中，請勿在 UI 中呈現架構例外狀況訊息或堆疊追蹤。 轉譯例外狀況訊息或堆疊追蹤可能：

* 將機密資訊洩漏給終端使用者。
* 協助惡意使用者探索應用程式中可能會危害應用程式、伺服器或網路安全性的弱點。

## <a name="global-exception-handling"></a>全域例外狀況處理

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

因為本節中的方法會使用語句來處理錯誤 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ，所以 SignalR 用戶端與伺服器之間的連接不會在發生錯誤且電路維持運作時中斷。 任何未處理的例外狀況對電路都是嚴重的。 如需詳細資訊，請參閱上一節，以瞭解 [ Blazor Server 應用程式如何回應未處理的例外](#how-a-blazor-server-app-reacts-to-unhandled-exceptions)狀況。

## <a name="log-errors-with-a-persistent-provider"></a>使用持續性提供者記錄錯誤

如果發生未處理的例外狀況，則會將例外狀況記錄到 <xref:Microsoft.Extensions.Logging.ILogger> 服務容器中設定的實例。 根據預設， Blazor 應用程式會使用主控台記錄提供者來記錄主控台輸出。 請考慮使用記錄管理檔大小和記錄檔的提供者，在伺服器上記錄更永久的位置。 或者，應用程式可以使用應用程式效能管理 (APM) 服務，例如 [Azure Application Insights (Azure 監視器) ](/azure/azure-monitor/app/app-insights-overview)。

在開發期間， Blazor Server 應用程式通常會將例外狀況的完整詳細資料傳送至瀏覽器的主控台，以協助進行偵錯工具。 在生產環境中，不會將詳細錯誤傳送給用戶端，但會在伺服器上記錄例外狀況的完整詳細資料。

您必須決定要記錄哪些事件，以及記錄事件的嚴重性層級。 惡意使用者可能可以刻意觸發錯誤。 例如，請勿從 `ProductId` 顯示產品詳細資料的元件 URL 中提供未知的錯誤記錄事件。 並非所有錯誤都應該視為記錄的事件。

如需詳細資訊，請參閱下列文章：

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

&dagger;適用于應用程式的 web API 後端應用程式的伺服器端 ASP.NET Core 應用程式 Blazor 。

## <a name="places-where-errors-may-occur"></a>可能發生錯誤的位置

架構和應用程式程式碼可能會在下列任何位置中觸發未處理的例外狀況，這會在本文的下列各節中進一步說明：

* [元件具現化](#component-instantiation-server)
* [生命週期方法](#lifecycle-methods-server)
* [轉譯邏輯](#rendering-logic-server)
* [事件處理常式](#event-handlers-server)
* [元件處置](#component-disposal-server)
* [JavaScript Interop](#javascript-interop-server)
* [Blazor Server 轉譯資料流程](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server">元件具現化</h3>

當 Blazor 建立元件的實例時：

* 會叫用元件的函式。
* 系統會叫用透過指示詞 [`@inject`](xref:mvc/views/razor#inject) 或[ `[Inject]` 屬性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供給元件之函式之任何非 singleton DI 服務的函式。

Blazor Server當任何已執行的函式或任何屬性的 setter 擲回 `[Inject]` 未處理的例外狀況時，電路會失敗。 例外狀況是嚴重的，因為架構無法將元件具現化。 如果函式邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。

<h3 id="lifecycle-methods-server">生命週期方法</h3>

在元件的存留期內，會叫用 Blazor [生命週期方法](xref:blazor/components/lifecycle#lifecycle-methods)。 如果任何生命週期方法會以同步或非同步方式擲回例外狀況，則例外狀況對電路而言是嚴重的 Blazor Server 。 若要讓元件處理生命週期方法中的錯誤，請新增錯誤處理邏輯。

在下列範例中，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 呼叫方法來取得產品：

* 方法中擲回的例外狀況 `ProductRepository.GetProductByIdAsync` 是由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句處理。
* `catch`執行區塊時：
  * `loadFailed` 設定為 `true` ，用來向使用者顯示錯誤訊息。
  * 會記錄錯誤。

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server">轉譯邏輯</h3>

元件檔 () 中的宣告式標記 Razor `.razor` 會編譯成名為的 c # 方法 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 。 當元件呈現時，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 執行並建立描述所轉譯元件之元素、文字和子元件的資料結構。

轉譯邏輯可能會擲回例外狀況。 當評估但為時，就會發生這種情況的範例 `@someObject.PropertyName` `@someObject` `null` 。 轉譯邏輯擲回的未處理例外狀況對電路而言是嚴重的 Blazor Server 。

若要避免轉譯 <xref:System.NullReferenceException> 邏輯中的，請 `null` 先檢查物件，再存取其成員。 在下列範例中， `person.Address` 如果是，則不會存取屬性 `person.Address` `null` ：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

上述程式碼假設 `person` 不是 `null` 。 程式碼的結構通常會保證物件存在於轉譯元件時。 在這些情況下，不需要檢查轉譯 `null` 邏輯。 在先前的範例中， `person` 可能保證存在，因為 `person` 會在元件具現化時建立，如下列範例所示：

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server">事件處理常式</h3>

使用下列專案建立事件處理常式時，用戶端程式代碼會觸發 c # 程式碼的調用：

* `@onclick`
* `@onchange`
* 其他 `@on...` 屬性
* `@bind`

在這些案例中，事件處理常式程式碼可能會擲回未處理的例外狀況。

如果事件處理常式擲回未處理的例外狀況 (例如，資料庫查詢) 失敗，則例外狀況對電路而言是嚴重的 Blazor Server 。 如果應用程式呼叫可能因為外部原因而失敗的程式碼，請使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。

如果使用者程式碼未攔截並處理例外狀況，則架構會記錄例外狀況並終止電路。

<h3 id="component-disposal-server">元件處置</h3>

您可以從 UI 中移除元件，例如，因為使用者已流覽至另一個頁面。 從 UI 中移除實作為的元件時 <xref:System.IDisposable?displayProperty=fullName> ，架構會呼叫元件的 <xref:System.IDisposable.Dispose%2A> 方法。

如果元件的方法擲回 `Dispose` 未處理的例外狀況，則例外狀況對線路而言是嚴重的 Blazor Server 。 如果處置邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。

如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。

<h3 id="javascript-interop-server">JavaScript Interop</h3>

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允許 .NET 程式碼在使用者的瀏覽器中對 JavaScript 執行時間進行非同步呼叫。

下列條件適用于的錯誤處理 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> ：

* 如果同步的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 失敗，就會發生 .net 例外狀況。 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為無法序列化提供的引數。 開發人員程式碼必須攔截例外狀況。 如果事件處理常式或元件生命週期方法中的應用程式程式碼未處理例外狀況，則產生的例外狀況對線路而言是嚴重的 Blazor Server 。
* 如果 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 非同步呼叫失敗，則 .net <xref:System.Threading.Tasks.Task> 會失敗。 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為 JavaScript 端程式碼會擲回例外狀況，或傳回 `Promise` 做為完成的 `rejected` 。 開發人員程式碼必須攔截例外狀況。 如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 運算子，請考慮使用錯誤處理和記錄，將方法呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中。 否則，失敗的程式碼會導致未處理的例外狀況對線路而言是嚴重的 Blazor Server 。
* 依預設，對的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 必須在特定期間內完成，否則會呼叫超時。預設的超時時間是一分鐘。 此超時會防止程式碼遺失網路連線或永遠不會傳回完成訊息的 JavaScript 程式碼。 如果呼叫超時，則產生的 <xref:System.Threading.Tasks> 會失敗，並出現 <xref:System.OperationCanceledException> 。 使用記錄來攔截和處理例外狀況。

同樣地，JavaScript 程式碼可能會起始對[ `[JSInvokable]` 屬性](xref:blazor/call-dotnet-from-javascript)所表示之 .net 方法的呼叫。 如果這些 .NET 方法擲回未處理的例外狀況：

* 例外狀況不會被視為線路的嚴重例外狀況 Blazor Server 。
* JavaScript 端 `Promise` 遭到拒絕。

您可以選擇在 .NET 端或方法呼叫的 JavaScript 端使用錯誤處理常式代碼。

如需詳細資訊，請參閱下列文章：

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server">Blazor Server 預</h3>

Blazor 您可以使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式來資源清單元件，以便在使用者的初始 HTTP 要求中傳回其轉譯的 HTML 標籤。 其運作方式如下：

* 針對屬於相同頁面的所有資源清單元件建立新的線路。
* 產生初始 HTML。
* 將線路視為 `disconnected` 使用者的瀏覽器會將 SignalR 連接重新建立回相同的伺服器。 建立連線時，會繼續執行線路的互動，並且更新元件的 HTML 標籤。

如果任何元件在轉譯期間擲回未處理的例外狀況，例如在生命週期方法或轉譯邏輯中：

* 線路的例外狀況是嚴重的。
* 例外狀況是從標記協助程式的呼叫堆疊中擲回 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 。 因此，整個 HTTP 要求都會失敗，除非開發人員程式碼明確攔截到例外狀況。

在正常情況下，預先轉譯失敗時，繼續建立和轉譯元件並不合理，因為無法轉譯工作元件。

若要容忍在預進行期間可能發生的錯誤，必須將錯誤處理邏輯放在可能會擲回例外狀況的元件內部。 使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句搭配錯誤處理和記錄。 您 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 可以將錯誤處理邏輯放在標記協助程式所呈現的元件中，而不是在語句中包裝標記協助程式 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 。

## <a name="advanced-scenarios"></a>進階案例

### <a name="recursive-rendering"></a>遞迴轉譯

元件可以用遞迴方式進行嵌套。 這適用于表示遞迴資料結構。 例如， `TreeNode` 元件可以 `TreeNode` 針對每個節點的子系呈現更多元件。

以遞迴方式呈現時，請避免產生無限遞迴的程式碼模式：

* 不要以遞迴方式呈現包含迴圈的資料結構。 例如，不要轉譯其子系本身包含的樹狀節點。
* 請勿建立包含迴圈的版面配置鏈。 例如，請勿建立版面配置本身的配置。
* 不允許終端使用者違反遞迴不變數 (規則) 透過惡意資料輸入或 JavaScript interop 呼叫。

轉譯期間的無限迴圈：

* 導致轉譯程式永遠繼續。
* 相當於建立未結束的迴圈。

在這些情況下，受影響的 Blazor Server 線路會失敗，而且執行緒通常會嘗試：

* 無限期耗用作業系統所允許的 CPU 時間。
* 耗用無限數量的伺服器記憶體。 使用無限制的記憶體相當於未結束的迴圈在每次反復專案中將專案新增至集合的案例。

若要避免無限遞迴模式，請確定遞迴轉譯程式碼包含適當的停止條件。

### <a name="custom-render-tree-logic"></a>自訂呈現樹狀結構邏輯

大部分的 Blazor 元件會實作為 Razor 元件檔 (`.razor`) ，並由架構編譯以產生可在上操作的邏輯， <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以呈現其輸出。 不過，開發人員可以使用程式化 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> c # 程式碼手動執行邏輯。 如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。

> [!WARNING]
> 手動轉譯樹狀結構產生器邏輯的使用會被視為 advanced 和 unsafe 案例，不建議用於一般元件開發。

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>撰寫程式碼時，開發人員必須保證程式碼的正確性。 例如，開發人員必須確保：

* 對 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的呼叫會正確平衡。
* 只會將屬性新增至正確的位置。

不正確的手動轉譯樹狀結構產生器邏輯可能會造成任何未定義的行為，包括損毀、伺服器當機和安全性弱點。

請考慮以相同的複雜度層級手動呈現樹狀檢視器邏輯，並以與撰寫元件程式碼或 [Microsoft 中繼語言 (MSIL](/dotnet/standard/managed-code)相同的 *危險* 層級，手動) 的指示。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

&dagger;適用于應用程式的 web API 後端應用程式的伺服器端 ASP.NET Core 應用程式 Blazor 。

::: zone-end
