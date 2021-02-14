---
title: ASP.NET Core 的威脅風險降低指導方針 Blazor Server
author: guardrex
description: 瞭解如何降低應用程式的安全性威脅 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 524a1f85838d083ed2f01afd96601bcd5e6ce12b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280973"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>ASP.NET Core 的威脅風險降低指導方針 Blazor Server

Blazor Server 應用程式採用具 *狀態* 的資料處理模型，其中伺服器和用戶端會維持長期的關聯性。 持續性狀態是由 [電路](xref:blazor/state-management)維護，它可以跨越也可能長期存在的連接。

當使用者造訪網站時 Blazor Server ，伺服器會在伺服器的記憶體中建立線路。 線路會向瀏覽器指出要轉譯的內容，並回應事件，例如當使用者在 UI 中選取按鈕時。 若要執行這些動作，線路會在使用者的瀏覽器中叫用 JavaScript 函式，並在伺服器上叫用 .NET 方法。 這種以 JavaScript 為基礎的雙向互動稱為 [javascript interop (JS interop) ](xref:blazor/call-javascript-from-dotnet)。

由於 JS interop 是在網際網路上進行，而且用戶端使用遠端瀏覽器，因此 Blazor Server 應用程式會共用大部分的 web 應用程式安全性考慮。 本主題說明對應用程式常見的威脅 Blazor Server ，並針對網際網路面向的應用程式提供威脅防護指引。

在受限制的環境中（例如公司網路或內部網路），部分風險降低指導方針包括：

* 不適用於受限的環境。
* 由於安全性風險在受限的環境中很低，因此不值得實行成本。

## <a name="blazor-and-shared-state"></a>Blazor 和共用狀態

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>資源耗盡

當用戶端與伺服器互動，而導致伺服器耗用過多資源時，就可能會發生資源耗盡。 過度耗用資源主要會影響：

* [CPU](#cpu)
* [記憶體](#memory)
* [用戶端連接](#client-connections)

拒絕服務 (DoS) 攻擊通常會試圖耗盡應用程式或伺服器的資源。 不過，資源耗盡不一定是系統攻擊的結果。 例如，有限的資源可能會因為高使用者需求而用盡。 DoS 將在 [拒絕服務 (dos) 的攻擊](#denial-of-service-dos-attacks) 一節中進一步討論。

架構外部的資源（ Blazor 例如資料庫和檔案控制代碼） (用來讀取和寫入檔案) ，也可能會遇到資源耗盡。 如需詳細資訊，請參閱<xref:performance/performance-best-practices>。

### <a name="cpu"></a>CPU

當一或多個用戶端強制服務器執行密集的 CPU 工作時，可能會發生 CPU 耗盡。

例如，假設有一個 Blazor Server 應用程式會計算 *Fibonnacci 數位*。 Fibonnacci 數位是從 Fibonnacci 序列產生的，其中序列中的每個數位都是上述兩個數字的總和。 到達答案所需的工作量取決於序列的長度和初始值的大小。 如果應用程式未對用戶端的要求進行限制，則需要大量 CPU 的計算可能會讓 CPU 的時間降低，並降低其他工作的效能。 過度耗用資源是安全性考慮，會影響可用性。

CPU 耗盡是所有公開應用程式的考慮。 在一般 web 應用程式中，要求和連接會以安全的形式 Blazor Server 提供，但應用程式不會提供相同的保護。 Blazor Server 應用程式必須包含適當的檢查和限制，才能執行可能需要大量 CPU 的工作。

### <a name="memory"></a>記憶體

當一或多個用戶端強制服務器耗用海量儲存體時，就可能會發生記憶體耗盡。

例如，請考慮 Blazor 具有接受並顯示專案清單之元件的-伺服器端應用程式。 如果 Blazor 應用程式未對允許的專案數目或轉譯回用戶端的專案數目有限制，記憶體密集的處理和轉譯可能會將伺服器的記憶體占到伺服器效能的時間點。 伺服器可能會損毀，或速度似乎損毀的點。

請考慮下列案例，以維護和顯示與伺服器上可能發生記憶體耗盡案例相關的專案清單：

* 屬性或欄位中的專案會 `List<MyItem>` 使用伺服器的記憶體。 如果應用程式允許專案清單成長到未系結，則會有伺服器記憶體不足的風險。 記憶體不足會導致目前的會話結束 (損毀) ，而且該伺服器實例中的所有並行會話都會收到記憶體不足的例外狀況。 若要避免發生此情況，應用程式必須使用在並行使用者上強加專案限制的資料結構。
* 如果沒有使用分頁配置來進行轉譯，伺服器會針對 UI 中看不到的物件使用額外的記憶體。 如果沒有專案數的限制，記憶體需求可能會耗盡可用的伺服器記憶體。 若要避免這種情況，請使用下列其中一種方法：
  * 轉譯時使用分頁清單。
  * 只顯示前100至1000個專案，並且要求使用者輸入搜尋準則，以在顯示的專案之外尋找專案。
  * 如需更先進的轉譯案例，請執行支援 *虛擬化* 的清單或格線。 使用虛擬化時，清單只會呈現使用者目前可見的專案子集。 當使用者與 UI 中的捲軸互動時，元件只會轉譯顯示所需的專案。 目前不需要顯示的專案可以保留在次要儲存體中，這是理想的方法。 Undisplayed 專案也可以保留在記憶體中，這比較不理想。

Blazor Server 應用程式會提供類似的程式設計模型給具狀態應用程式的其他 UI 架構，例如 WPF、Windows Forms 或 Blazor WebAssembly 。 主要的差異在於，應用程式所耗用的記憶體屬於用戶端，而且只會影響該個別用戶端的幾個 UI 架構。 例如， Blazor WebAssembly 應用程式會完全在用戶端上執行，而且只會使用用戶端記憶體資源。 在此 Blazor Server 案例中，應用程式所耗用的記憶體屬於伺服器，並在伺服器實例上的用戶端間共用。

伺服器端記憶體需求是所有應用程式的考慮 Blazor Server 。 不過，大部分的 web 應用程式都是無狀態的，而且在處理要求時使用的記憶體會在傳迴響應時釋放。 作為一般建議，請不要讓用戶端配置未系結的記憶體數量，就像在任何其他可保存用戶端連接的伺服器端應用程式中一樣。 應用程式所耗用的記憶體會 Blazor Server 保存超過單一要求的時間。

> [!NOTE]
> 在開發期間，您可以流量分析工具或捕獲的追蹤，來評估用戶端的記憶體需求。 Profiler 或追蹤不會捕捉配置給特定用戶端的記憶體。 若要在開發期間捕捉特定用戶端的記憶體使用量，請捕獲傾印，並檢查所有根于使用者線路之物件的記憶體需求。

### <a name="client-connections"></a>用戶端連接

當一或多個用戶端開啟太多與伺服器的並行連線時，就會發生連接耗盡，而導致其他用戶端無法建立新的連線。

Blazor 用戶端會針對每個會話建立單一連線，只要開啟瀏覽器視窗，就會保持開啟連接。 維護所有連線之伺服器上的需求並非 Blazor 應用程式特有的。 由於連線的持續性本質和應用程式的具狀態本質 Blazor Server ，連接耗盡對於應用程式的可用性有較大的風險。

依預設，應用程式的每個使用者連線數目沒有限制 Blazor Server 。 如果應用程式需要連線限制，請採取下列一或多個方法：

* 需要驗證，這會自然地限制未授權使用者連線到應用程式的能力。 為了讓此案例生效，使用者必須防止使用者布建新的使用者。
* 限制每個使用者的連接數目。 您可以透過下列方法來限制連接。 請小心讓合法的使用者存取應用程式 (例如，根據用戶端的 IP 位址) 建立連線限制。
  * 在應用層級：
    * 端點路由擴充性。
    * 需要驗證才能連線到應用程式，並追蹤每位使用者的作用中會話。
    * 在達到限制時拒絕新的會話。
    * Proxy WebSocket 透過使用 proxy 連線到應用程式，例如將分離信號從用戶端連線到應用程式的 [Azure SignalR 服務](/azure/azure-signalr/signalr-overview) 。 這會提供比單一用戶端可以建立的連接容量更大的應用程式，以防止用戶端耗盡與伺服器的連線。
  * 在伺服器層級：在應用程式前面使用 proxy/閘道。 例如， [Azure Front Door](/azure/frontdoor/front-door-overview) 可讓您定義、管理及監視應用程式之 web 流量的全域路由，並在 Blazor Server 應用程式設定為使用長時間輪詢時運作。
  
    > [!NOTE]
    > 雖然應用程式支援長時間輪詢 Blazor Server ，但 [websocket 是建議的傳輸通訊協定](xref:blazor/host-and-deploy/server#azure-signalr-service)。 [Azure Front Door](/azure/frontdoor/front-door-overview) 目前不支援 websocket，但在未來的服務版本中，會針對 websocket 的支援進行考慮。

## <a name="denial-of-service-dos-attacks"></a>拒絕服務 (DoS) 的攻擊

阻絕服務 (DoS) 攻擊涉及用戶端，導致伺服器耗盡其一或多個資源，使應用程式無法使用。 Blazor Server 應用程式包含預設限制，並且依賴其他 ASP.NET Core 和 SignalR 限制，以防止在上設定的 DoS 攻擊 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> ：

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>

如需詳細資訊和設定程式碼撰寫範例，請參閱下列文章：

* <xref:blazor/fundamentals/signalr>
* <xref:signalr/configuration>

## <a name="interactions-with-the-browser-client"></a>與瀏覽器 (用戶端) 的互動

用戶端會透過 JS interop 事件分派和轉譯完成來與伺服器互動。 JS interop 通訊會在 JavaScript 和 .NET 之間進行這兩種方式：

* 瀏覽器事件是以非同步方式從用戶端分派至伺服器。
* 伺服器會視需要以非同步方式轉譯資料流程 UI。

### <a name="javascript-functions-invoked-from-net"></a>從 .NET 叫用的 JavaScript 函式

針對從 .NET 方法到 JavaScript 的呼叫：

* 所有調用都有可設定的超時時間，在這之後會將 <xref:System.OperationCanceledException> 傳回給呼叫端。
  * 呼叫 (的預設超時時間 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) 一分鐘。 若要設定此限制，請參閱 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> 。
  * 您可以提供解除標記來控制每個呼叫的取消。 如果有提供取消權杖，就會依賴預設的呼叫超時時間，在可能的情況下對用戶端的任何呼叫進行時間限制。
* 無法信任 JavaScript 呼叫的結果。 Blazor在瀏覽器中執行的應用程式用戶端會搜尋要叫用的 JavaScript 函式。 系統會叫用此函式，並產生結果或錯誤。 惡意用戶端可以嘗試：
  * 從 JavaScript 函式傳回錯誤，導致應用程式發生問題。
  * 藉由從 JavaScript 函式傳回非預期的結果，在伺服器上引發非預期的行為。

請採取下列預防措施來防範上述案例：

* 將 JS interop 呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中，以考慮在調用期間可能發生的錯誤。 如需詳細資訊，請參閱<xref:blazor/fundamentals/handle-errors#javascript-interop>。
* 在採取任何動作之前，請先驗證從 JS interop 調用傳回的資料（包括錯誤訊息）。

### <a name="net-methods-invoked-from-the-browser"></a>從瀏覽器叫用的 .NET 方法

請勿信任從 JavaScript 到 .NET 方法的呼叫。 當 .NET 方法公開至 JavaScript 時，請考慮如何叫用 .NET 方法：

* 將公開給 JavaScript 的任何 .NET 方法視為應用程式的公用端點。
  * 驗證輸入。
    * 請確認值在預期的範圍內。
    * 確定使用者有權執行所要求的動作。
  * 請勿在 .NET 方法調用中配置過多的資源數量。 例如，執行對 CPU 和記憶體使用量的檢查和位置限制。
  * 請考慮靜態和實例方法可以公開給 JavaScript 用戶端。 除非設計呼叫具有適當條件約束的共用狀態，否則請避免在會話之間共用狀態。
    * 針對透過相依性插入 (DI) 所公開的物件所公開的實例方法 `DotNetReference` ，物件應該註冊為已設定範圍的物件。 這適用于應用程式使用的任何 DI 服務 Blazor Server 。
    * 針對靜態方法，除非應用程式在伺服器實例上的所有使用者之間明確共用狀態，否則請避免建立無法範圍設定為用戶端的狀態。
  * 避免將使用者提供的資料在參數中傳遞給 JavaScript 呼叫。 如果在參數中傳遞資料是絕對必要的，請確定 JavaScript 程式碼會處理資料的傳遞，而不會引進 [跨網站腳本 (XSS) ](#cross-site-scripting-xss) 弱點。 例如，請不要藉由設定專案的屬性，將使用者提供的資料寫入檔物件模型 (DOM) `innerHTML` 。 請考慮使用 [ (CSP) 的內容安全性原則 ](https://developer.mozilla.org/docs/Web/HTTP/CSP) 來停用 `eval` 和其他不安全的 JavaScript 基本專案。
* 避免在架構的分派實行之上，執行 .NET 調用的自訂分派。 將 .NET 方法公開給瀏覽器是一種 advanced 案例，不建議用於一般 Blazor 開發。

### <a name="events"></a>事件

事件會提供應用程式的進入點 Blazor Server 。 在 web 應用程式中保護端點的相同規則適用于應用程式中的事件處理 Blazor Server 。 惡意用戶端可以傳送任何想要傳送的資料做為事件的承載。

例如：

* 的變更事件 `<select>` 可能會傳送不在應用程式呈現給用戶端之選項中的值。
* 可 `<input>` 將任何文字資料傳送至伺服器，略過用戶端驗證。

應用程式必須驗證應用程式處理之任何事件的資料。 Blazor架構[表單元件](xref:blazor/forms-validation)會執行基本驗證。 如果應用程式使用自訂表單元件，則必須撰寫自訂程式碼來適當地驗證事件資料。

Blazor Server 事件是非同步，因此，您可以在應用程式有時間產生新的轉譯之前，將多個事件分派至伺服器。 這有一些要考慮的安全性含意。 限制應用程式中的用戶端動作必須在事件處理常式內執行，而不需依賴目前呈現的檢視狀態。

請考慮應該允許使用者將計數器最多遞增三次的 counter 元件。 根據下列值，有條件地遞增計數器的按鈕 `count` ：

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

用戶端可以分派一或多個增量事件，架構才會產生這個元件的新轉譯。 結果是， `count` 使用者可以增加 *三次* ，因為 UI 很快就不會移除按鈕。 下列範例顯示可達成三個增量限制的正確方式 `count` ：

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

藉由在 `if (count < 3) { ... }` 處理常式內新增檢查，將 `count` 會根據目前的應用程式狀態來遞增決定。 這項決策並不是以上述範例中的 UI 狀態為基礎，這可能會暫時過時。

### <a name="guard-against-multiple-dispatches"></a>防止多個分派

如果事件回呼以非同步方式叫用長時間執行的作業，例如從外部服務或資料庫提取資料，請考慮使用防護。 當作業正在進行視覺效果的意見反應時，此防護可以防止使用者將多個作業排入佇列。 `isLoading` `true` 從伺服器取得資料時，下列元件程式碼會將設定為 `GetForecastAsync` 。 當 `isLoading` 為時 `true` ，UI 中的按鈕會停用：

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

如果使用模式以非同步方式執行背景作業，則在上述範例中所示範的防護模式會正常運作 `async` - `await` 。

### <a name="cancel-early-and-avoid-use-after-dispose"></a>提早取消並避免使用-dispose

除了使用「 [針對多個分派進行防護](#guard-against-multiple-dispatches) 」一節中所述的防護之外，請考慮使用在 <xref:System.Threading.CancellationToken> 元件處置時取消長時間執行的作業。 這種方法有額外的好處，就是避免在元件中 *使用-after 處置* ：

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>避免產生大量資料的事件

某些 DOM 事件（例如 `oninput` 或 `onscroll` ）可能會產生大量的資料。 避免在伺服器應用程式中使用這些事件 Blazor 。

## <a name="additional-security-guidance"></a>其他安全性指引

適用于保護 ASP.NET Core apps 的指導方針適用于應用 Blazor Server 程式，並在下列各節中討論：

* [記錄和敏感性資料](#logging-and-sensitive-data)
* [使用 HTTPS 保護傳輸中的資訊](#protect-information-in-transit-with-https)
* [跨網站腳本 (XSS) ](#cross-site-scripting-xss)
* [跨原始來源保護](#cross-origin-protection)
* [按一下-劫持](#click-jacking)
* [開啟重新導向](#open-redirects)

### <a name="logging-and-sensitive-data"></a>記錄和敏感性資料

用戶端與伺服器之間的 JS interop 互動會記錄在具有實例的伺服器記錄中 <xref:Microsoft.Extensions.Logging.ILogger> 。 Blazor 避免記錄敏感性資訊，例如實際事件或 JS interop 輸入和輸出。

當伺服器上發生錯誤時，架構會通知用戶端，並向下眼淚會話。 根據預設，用戶端會收到一般錯誤訊息，可以在瀏覽器的開發人員工具中看到。

用戶端錯誤不包含呼叫堆疊，也不會提供錯誤原因的詳細資料，但伺服器記錄檔包含這類資訊。 基於開發目的，可透過 [啟用詳細錯誤](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors)，讓用戶端可以使用敏感性錯誤資訊。

> [!WARNING]
> 將錯誤資訊公開給網際網路上的用戶端，應該一律避免使用安全性風險。

### <a name="protect-information-in-transit-with-https"></a>使用 HTTPS 保護傳輸中的資訊

Blazor ServerSignalR用於用戶端與伺服器之間的通訊。 Blazor Server 通常會使用協調的傳輸 SignalR ，這通常是 websocket。

Blazor Server 不確定在伺服器與用戶端之間傳送之資料的完整性和機密性。 一律使用 HTTPS。

### <a name="cross-site-scripting-xss"></a>跨網站腳本 (XSS) 

跨網站腳本 (XSS) 可讓未經授權的合作物件在瀏覽器的內容中執行任意邏輯。 遭入侵的應用程式可能會在用戶端上執行任意程式碼。 此弱點可能會用來對伺服器執行一些惡意動作：

* 將假/無效事件分派至伺服器。
* 分派失敗/不正確轉譯完成。
* 避免分派轉譯完成。
* 從 JavaScript 將 interop 呼叫分派至 .NET。
* 修改從 .NET 到 JavaScript 之 interop 呼叫的回應。
* 避免將 .NET 分派至 JS interop 結果。

Blazor Server架構會採取下列步驟來防範先前的威脅：

* 如果用戶端未認可轉譯批次，就會停止產生新的 UI 更新。 設定為 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> 。
* 在一分鐘後，任何 .NET 至 JavaScript 呼叫都不會收到來自用戶端的回應。 設定為 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> 。
* 針對在 JS interop 期間來自瀏覽器的所有輸入執行基本驗證：
  * .NET 參考是有效的，而且是 .NET 方法所需的型別。
  * 資料的格式不正確。
  * 此方法的正確引數數目存在於裝載中。
  * 在叫用方法之前，可以正確還原序列化引數或結果。
* 針對來自已分派事件之瀏覽器的所有輸入，執行基本驗證：
  * 事件具有有效的型別。
  * 事件的資料可以還原序列化。
  * 有與事件相關聯的事件處理常式。

除了架構所實行的保護，應用程式必須由開發人員撰寫程式碼，以防範威脅並採取適當的動作：

* 處理事件時，一律驗證資料。
* 在接收無效資料時採取適當的動作：
  * 略過資料並返回。 這可讓應用程式繼續處理要求。
  * 如果應用程式判斷輸入是非法的，且無法由合法用戶端產生，則會擲回例外狀況。 擲回例外狀況會向下眼淚，並結束會話。
* 請勿信任記錄中包含的轉譯批次完成所提供的錯誤訊息。 此錯誤是由用戶端提供，且通常無法信任，因為用戶端可能遭到入侵。
* 請勿在 JavaScript 與 .NET 方法之間的任何一種方向信任 JS interop 呼叫的輸入。
* 應用程式會負責驗證引數和結果的內容是否有效，即使引數或結果已正確還原序列化。

若要讓 XSS 弱點存在，應用程式必須將使用者輸入併入轉譯的頁面中。 Blazor Server 元件會執行編譯時期步驟，其中檔案中的標記 `.razor` 會轉換成程式的 c # 邏輯。 在執行時間，c # 邏輯會建立轉譯 *樹狀結構* ，以描述元素、文字和子元件。 這會透過 JavaScript 指令的序列套用至瀏覽器的 DOM， (，或在進行) 的呈現時，會序列化為 HTML：

* 透過一般 Razor 語法所呈現的使用者輸入 (例如， `@someStringValue`) 不會公開 XSS 弱點，因為 Razor 語法是透過只能寫入文字的命令新增至 DOM。 即使值包含 HTML 標籤，值也會顯示為靜態文字。 當預先呈現時，輸出會以 HTML 編碼，也會將內容顯示為靜態文字。
* 不允許腳本標記，也不應該包含在應用程式的元件呈現樹狀結構中。 如果腳本標記包含在元件的標記中，則會產生編譯階段錯誤。
* 元件作者可以在 c # 中撰寫元件，而不需要使用 Razor 。 元件作者負責在發出輸出時使用正確的 Api。 例如，使用 `builder.AddContent(0, someUserSuppliedString)` 而 *非* `builder.AddMarkupContent(0, someUserSuppliedString)` ，因為後者可能會產生 XSS 弱點。

在保護 XSS 攻擊的過程中，請考慮執行 XSS 防護措施，例如 [ (CSP) 的內容安全性原則 ](https://developer.mozilla.org/docs/Web/HTTP/CSP)。

如需詳細資訊，請參閱<xref:security/cross-site-scripting>。

### <a name="cross-origin-protection"></a>跨原始來源保護

跨原始來源攻擊涉及來自不同來源的用戶端，對伺服器執行動作。 惡意動作通常是 GET 要求或表單 POST (的跨網站要求偽造、CSRF) ，但也可能會開啟惡意 WebSocket。 Blazor Server 應用程式 [會提供與任何其他 SignalR 使用中樞通訊協定的應用程式相同的保證](xref:signalr/security)：

* Blazor Server 應用程式可以跨原始來源存取，除非採取其他措施來防止此應用程式。 若要停用跨原始來源存取，請停用端點中的 CORS，方法是將 CORS 中介軟體新增至管線，並將加入 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 至 Blazor 端點中繼資料，或藉由設定 [ SignalR 跨原始來源資源分享](xref:signalr/security#cross-origin-resource-sharing)來限制允許的來源集合。
* 如果已啟用 CORS，可能需要額外的步驟，以根據 CORS 設定來保護應用程式。 如果 CORS 是全域啟用的，則在呼叫端點路由產生器之後，您可以將 Blazor Server <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 中繼資料新增至端點中繼資料，以停用中樞的 cors <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 。

如需詳細資訊，請參閱<xref:security/anti-request-forgery>。

### <a name="click-jacking"></a>按一下-劫持

按一下-劫持牽涉 `<iframe>` 到從不同的來源將網站轉譯為網站內的網站，以誘騙使用者在遭受攻擊的網站上執行動作。

若要防止應用程式在中轉譯 `<iframe>` ，請使用 [內容安全性原則 (CSP) ](https://developer.mozilla.org/docs/Web/HTTP/CSP) 和 `X-Frame-Options` 標頭。 如需詳細資訊，請參閱 [MDN web 檔： X 框架選項](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。

### <a name="open-redirects"></a>開啟重新導向

當 Blazor Server 應用程式會話啟動時，伺服器會針對啟動會話時所傳送的 url 執行基本驗證。 架構會在建立線路之前，檢查基底 URL 是否為目前 URL 的父系。 架構不會執行其他檢查。

當使用者選取用戶端上的連結時，連結的 URL 會傳送至伺服器，以決定要採取的動作。 例如，應用程式可能會執行用戶端導覽，或向瀏覽器表示移至新位置。

元件也可以透過使用，以程式設計方式觸發流覽要求 <xref:Microsoft.AspNetCore.Components.NavigationManager> 。 在這種情況下，應用程式可能會執行用戶端導覽，或向瀏覽器表示移至新的位置。

元件必須：

* 避免使用使用者輸入做為導覽呼叫引數的一部分。
* 驗證引數，以確保應用程式允許該目標。

否則，惡意使用者可以強制瀏覽器移至攻擊者控制的網站。 在此案例中，攻擊者會將應用程式訣竅為使用部分使用者輸入做為方法調用的一部分 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。

當轉譯連結作為應用程式的一部分時，也適用這項建議：

* 可能的話，請使用相對連結。
* 先驗證絕對連結目的地是否有效，再將它們包含在頁面中。

如需詳細資訊，請參閱<xref:security/preventing-open-redirects>。

## <a name="security-checklist"></a>安全性檢查清單

下列安全性考慮清單不完整：

* 驗證事件的引數。
* 從 JS interop 呼叫驗證輸入和結果。
* 請避免使用 (，或事先驗證 .NET to JS interop 呼叫的使用者輸入) 。
* 防止用戶端配置未系結的記憶體量。
  * 元件中的資料。
  * `DotNetObject` 傳回給用戶端的參考。
* 防止多個分派。
* 在處置元件時，取消長時間執行的作業。
* 避免產生大量資料的事件。
* 如果無法避免，請避免在呼叫中使用使用者輸入 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ，並針對一組允許的來源驗證使用者輸入。
* 請勿根據 UI 的狀態，但只能從元件狀態進行授權決策。
* 請考慮使用 [內容安全性原則 (CSP) ](https://developer.mozilla.org/docs/Web/HTTP/CSP) 來防範 XSS 攻擊。
* 請考慮使用 CSP 和 [X 框架選項](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) ，以防止按一下劫持。
* 啟用 CORS 或明確停用應用程式的 CORS 時，請確定 CORS 設定適當 Blazor 。
* 測試以確保應用程式的伺服器端限制 Blazor 提供可接受的使用者體驗，而不會有無法接受的風險層級。
