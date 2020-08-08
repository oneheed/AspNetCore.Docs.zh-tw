---
title: ASP.NET Core Blazor 狀態管理
author: guardrex
description: 瞭解如何在應用程式中保存狀態 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 28ca3b5c4472dc21e709d01705dc64168107ca61
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013550"
---
# <a name="aspnet-core-no-locblazor-state-management"></a>ASP.NET Core Blazor 狀態管理

作者： [Steve Sanderson](https://github.com/SteveSandersonMS)和[Luke Latham](https://github.com/guardrex)

::: zone pivot="webassembly"

在應用程式中建立的使用者狀態 Blazor WebAssembly 會保留在瀏覽器的記憶體中。

瀏覽器記憶體中所保存的使用者狀態範例包括：

* 呈現的 UI 中元件實例及其最新轉譯輸出的階層。
* 元件實例中的欄位和屬性值。
* 保存在相依性[插入 (DI) ](xref:fundamentals/dependency-injection)服務實例中的資料。
* 透過[JavaScript interop](xref:blazor/call-javascript-from-dotnet)呼叫所設定的值。

當使用者關閉並重新開啟其瀏覽器或重載頁面時，會遺失瀏覽器記憶體中所保存的使用者狀態。

## <a name="persist-state-across-browser-sessions"></a>跨瀏覽器會話保存狀態

一般來說，會在使用者主動建立資料的瀏覽器會話之間維護狀態，而不只是讀取已經存在的資料。

若要在瀏覽器會話之間保留狀態，應用程式必須將資料保存在瀏覽器記憶體以外的其他儲存位置。 狀態持續性不是自動的。 開發應用程式時，您必須採取步驟來執行具狀態的資料持續性。

資料持續性通常只有在使用者花費在建立的高價值狀態時才需要。 在下列範例中，保存狀態可以節省商務工作的時間或輔助：

* 多步驟 web 表單：使用者在其狀態遺失時，針對多步驟 web 表單的幾個完成步驟重新輸入資料相當耗時。 如果使用者離開表單並于稍後返回，則會在此案例中失去狀態。
* 購物車：可以維護應用程式中代表潛在收益的任何商業重要元件。 失去其狀態的使用者，因此其購物車，可能會在日後返回網站時購買較少的產品或服務。

應用程式只能保存*應用程式狀態*。 Ui 無法保存，例如元件實例和其呈現樹狀結構。 元件和轉譯樹狀結構通常不是可序列化的。 若要保存 UI 狀態（例如樹狀檢視控制項的展開節點），應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。

## <a name="where-to-persist-state"></a>保存狀態的位置

保存狀態有三個常見的位置：

* [伺服器端儲存體](#server-side-storage)
* [URL](#url)
* [瀏覽器儲存](#browser-storage)

### <a name="server-side-storage"></a>伺服器端儲存體

對於跨越多個使用者和裝置的永久資料持續性，應用程式可以使用透過 Web API 存取的獨立伺服器端儲存體。 選項包括：

* Blob 儲存體
* 索引鍵/值儲存
* 關聯式資料庫
* 表格儲存體

儲存資料之後，使用者的狀態會保留，並可在任何新的瀏覽器會話中使用。

因為 Blazor WebAssembly 應用程式完全在使用者的瀏覽器中執行，所以它們需要額外的措施來存取安全的外部系統，例如儲存體服務和資料庫。 Blazor WebAssembly應用程式的保護方式與單一頁面應用程式 (Spa) 相同。 一般而言，應用程式會透過[OAuth](https://oauth.net) / [OpenID connect (OIDC) ](https://openid.net/connect/)來驗證使用者，然後藉由 Web API 對伺服器端應用程式的呼叫，與儲存體服務和資料庫互動。 伺服器端應用程式會調節 Blazor WebAssembly 應用程式與儲存體服務或資料庫之間的資料傳輸。 Blazor WebAssembly應用程式會維護與伺服器端應用程式的暫時連線，而伺服器端應用程式會持續連線至儲存體。

如需詳細資訊，請參閱下列資源：

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*安全性與 Identity *關於

如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：

* [Azure 資料庫](https://azure.microsoft.com/product-categories/databases/)
* [Azure 儲存體檔](/azure/storage/)

### <a name="url"></a>URL

對於代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。 在 URL 中模型化使用者狀態的範例包括：

* 已查看之實體的識別碼。
* 分頁方格中的目前頁碼。

如果使用者手動重載頁面，則會保留瀏覽器網址列的內容。

如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。

### <a name="browser-storage"></a>瀏覽器儲存

對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器的 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：

* `localStorage`的範圍是瀏覽器的視窗。 如果使用者重載頁面，或關閉並重新開啟瀏覽器，則狀態會保持不變。 如果使用者開啟多個瀏覽器索引標籤，則狀態會在索引標籤之間共用。 資料會保存在中， `localStorage` 直到明確清除為止。
* `sessionStorage`的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載索引標籤，則狀態會保持不變。 如果使用者關閉索引標籤或瀏覽器，則狀態會遺失。 如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。

> [!NOTE]
> `localStorage`和 `sessionStorage` 可以在應用程式中使用， Blazor WebAssembly 但只能透過撰寫自訂程式碼或使用協力廠商套件。

一般而言， `sessionStorage` 使用較安全。 `sessionStorage`避免使用者開啟多個索引標籤並遇到下列問題的風險：

* 索引標籤上狀態儲存體中的 bug。
* 當索引標籤覆寫其他索引標籤的狀態時，會造成混淆的行為。

`localStorage`如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，這是較好的選擇。

> [!WARNING]
> 使用者可能會看到或篡改儲存在和中的資料 `localStorage` `sessionStorage` 。

## <a name="additional-resources"></a>其他資源

* [在驗證操作之前儲存應用程式狀態](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server是可設定狀態的應用程式架構。 在大部分的情況下，應用程式會維護與伺服器的連接。 使用者的狀態會保留在*伺服器的記憶體中。* 

線路中保留的使用者狀態範例包括：

* 呈現的 UI 中元件實例及其最新轉譯輸出的階層。
* 元件實例中的欄位和屬性值。
* 在相依性插入中保存的資料[ (DI) ](xref:fundamentals/dependency-injection)範圍設定為線路的服務實例。

您也可以透過[javascript interop](xref:blazor/call-javascript-from-dotnet)呼叫，在瀏覽器記憶體集的 javascript 變數中找到使用者狀態。

如果使用者遇到暫時性的網路連線遺失， Blazor 會嘗試將使用者重新連接到其原始狀態的原始線路。 不過，不一定能夠將使用者重新連接到伺服器記憶體中的原始線路：

* 伺服器無法永久保留中斷連線的線路。 伺服器必須在超時之後或伺服器處於記憶體不足的壓力時，釋放中斷連線的線路。
* 在多伺服器、負載平衡的部署環境中，當不再需要處理要求的整體量時，個別伺服器可能會失敗或自動移除。 使用者嘗試重新連線時，對使用者的源伺服器處理要求可能會變成無法使用。
* 使用者可能會關閉並重新開啟其瀏覽器或重載頁面，這會移除瀏覽器記憶體中保留的任何狀態。 例如，透過 JavaScript interop 呼叫所設定的 JavaScript 變數值會遺失。

當使用者無法重新連線到其原始線路時，使用者會收到空白狀態的新線路。 這相當於關閉和重新開啟桌面應用程式。

## <a name="persist-state-across-circuits"></a>跨線路保存狀態

一般來說，會在使用者主動建立資料的線路上維護狀態，而不只是讀取已經存在的資料。

若要保留跨線路的狀態，應用程式必須將資料保存在伺服器記憶體以外的其他儲存位置。 狀態持續性不是自動的。 開發應用程式時，您必須採取步驟來執行具狀態的資料持續性。

資料持續性通常只有在使用者花費在建立的高價值狀態時才需要。 在下列範例中，保存狀態可以節省商務工作的時間或輔助：

* 多步驟 web 表單：使用者在其狀態遺失時，針對多步驟 web 表單的幾個完成步驟重新輸入資料相當耗時。 如果使用者離開表單並于稍後返回，則會在此案例中失去狀態。
* 購物車：可以維護應用程式中代表潛在收益的任何商業重要元件。 失去其狀態的使用者，因此其購物車，可能會在日後返回網站時購買較少的產品或服務。

應用程式只能保存*應用程式狀態*。 Ui 無法保存，例如元件實例和其呈現樹狀結構。 元件和轉譯樹狀結構通常不是可序列化的。 若要保存 UI 狀態（例如樹狀檢視控制項的展開節點），應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。

## <a name="where-to-persist-state"></a>保存狀態的位置

保存狀態有三個常見的位置：

* [伺服器端儲存體](#server-side-storage)
* [URL](#url)
* [瀏覽器儲存](#browser-storage)

### <a name="server-side-storage"></a>伺服器端儲存體

對於跨越多個使用者和裝置的永久資料持續性，應用程式可以使用伺服器端儲存體。 選項包括：

* Blob 儲存體
* 索引鍵/值儲存
* 關聯式資料庫
* 表格儲存體

儲存資料之後，使用者的狀態會保留，並可在任何新的線路中使用。

如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：

* [Azure 資料庫](https://azure.microsoft.com/product-categories/databases/)
* [Azure 儲存體檔](/azure/storage/)

### <a name="url"></a>URL

對於代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。 在 URL 中模型化使用者狀態的範例包括：

* 已查看之實體的識別碼。
* 分頁方格中的目前頁碼。

瀏覽器的網址列內容會保留：

* 如果使用者手動重載頁面。
* 如果網頁伺服器變得無法使用，而且會強制使用者重載頁面，以便連接到不同的伺服器。

如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。

### <a name="browser-storage"></a>瀏覽器儲存

對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器的 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：

* `localStorage`的範圍是瀏覽器的視窗。 如果使用者重載頁面，或關閉並重新開啟瀏覽器，則狀態會保持不變。 如果使用者開啟多個瀏覽器索引標籤，則狀態會在索引標籤之間共用。 資料會保存在中， `localStorage` 直到明確清除為止。
* `sessionStorage`的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載索引標籤，則狀態會保持不變。 如果使用者關閉索引標籤或瀏覽器，則狀態會遺失。 如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。

一般而言， `sessionStorage` 使用較安全。 `sessionStorage`避免使用者開啟多個索引標籤並遇到下列問題的風險：

* 索引標籤上狀態儲存體中的 bug。
* 當索引標籤覆寫其他索引標籤的狀態時，會造成混淆的行為。

`localStorage`如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，這是較好的選擇。

使用瀏覽器儲存的注意事項：

* 類似于使用伺服器端資料庫，載入和儲存資料是非同步。
* 與伺服器端資料庫不同的是，預先呈現期間無法使用儲存體，因為在預先呈現階段期間，瀏覽器中不存在要求的頁面。
* 儲存幾 kb 的資料可合理保存 Blazor Server 應用程式。 除了幾 kb 以外，您還必須考慮效能上的影響，因為資料是透過網路載入和儲存。
* 使用者可能會看到或篡改資料。 [ASP.NET Core 資料保護](xref:security/data-protection/introduction)可以降低風險。 例如， [ASP.NET Core 受保護的瀏覽器存放裝置](#aspnet-core-protected-browser-storage)使用 ASP.NET Core 的資料保護。

協力廠商 NuGet 套件提供使用和的 Api `localStorage` `sessionStorage` 。 值得考慮選擇以透明方式使用[ASP.NET Core 資料保護](xref:security/data-protection/introduction)的套件。 資料保護會將儲存的資料加密，並降低篡改已儲存資料的潛在風險。 如果 JSON 序列化資料是以純文字儲存，則使用者可以使用瀏覽器開發人員工具來查看資料，同時也會修改儲存的資料。 保護資料不一定會有問題，因為資料在本質上可能是很簡單的。 例如，讀取或修改 UI 專案的預存色彩對使用者或組織而言並不會有嚴重的安全性風險。 避免允許使用者檢查或篡改*敏感性資料*。

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET Core 受保護的瀏覽器存放裝置

ASP.NET Core 受保護的瀏覽器存放裝置會利用與[ASP.NET Core 的資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。

> [!NOTE]
> 受保護的瀏覽器存放裝置依賴 ASP.NET Core 資料保護，且僅支援 Blazor Server 應用程式。

### <a name="configuration"></a>組態

1. 將套件參考新增至 [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) 。
1. 在中 `Startup.ConfigureServices` ，呼叫 `AddProtectedBrowserStorage` 以將 `localStorage` 和服務新增 `sessionStorage` 至服務集合：

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>儲存和載入元件中的資料

在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

選擇取決於您想要使用的瀏覽器儲存位置。 在下列範例中， `sessionStorage` 會使用：

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

指示詞 `@using` 可以放在應用程式的檔案中， `_Imports.razor` 而不是在元件中。 使用檔案 `_Imports.razor` 會讓應用程式或整個應用程式的較大區段可以使用命名空間。

若要根據 `currentCount` 專案範本，將值保存在 `Counter` 應用程式的元件中 Blazor Server ，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

在更大、更實際的應用程式中，個別欄位的儲存是不太可能發生的情況。 應用程式較可能儲存包含複雜狀態的整個模型物件。 `ProtectedSessionStore`自動序列化和還原序列化 JSON 資料，以儲存複雜的狀態物件。

在上述程式碼範例中， `currentCount` 資料會 `sessionStorage['count']` 在使用者的瀏覽器中儲存為。 資料不會以純文字儲存，而是使用 ASP.NET Core 資料保護來保護。 如果 `sessionStorage['count']` 是在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。

若要 `currentCount` 在使用者稍後回到元件時復原資料 `Counter` ，包括使用者是否在新的線路上，請使用 `ProtectedSessionStore.GetAsync` ：

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

如果元件的參數包含導覽狀態，請呼叫， `ProtectedSessionStore.GetAsync` 並 `null` 在中指派非結果 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>只有在第一次具現化元件時，才會呼叫。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>如果使用者流覽至不同的 URL，但仍在相同頁面上，則不會再呼叫一次。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。

> [!WARNING]
> 此章節中的範例僅適用于伺服器未啟用預先安裝的情況。 啟用自動處理時，會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。
>
> 請停用已選擇的，或加入額外的程式碼以使用可處理的。 若要深入瞭解如何撰寫可搭配已預呈現運作的程式碼，請參閱[處理預呈現](#handle-prerendering)一節。

### <a name="handle-the-loading-state"></a>處理載入狀態

由於瀏覽器存放裝置是透過網路連接以非同步方式存取，因此在載入資料並提供給元件之前，一律會有一段時間。 若要獲得最佳結果，請在載入進行時轉譯載入狀態訊息，而不是顯示空白或預設的資料。

其中一種方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。 在預設 `Counter` 元件中，計數會保留在中 `int` 。 將問號 () 加入 () 的類型，[使其成為 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：

```csharp
private int? currentCount;
```

若不是無條件顯示計數和 **`Increment`** 按鈕，只會在透過檢查來載入資料時，才顯示這些元素 <xref:System.Nullable%601.HasValue%2A> ：

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>處理已預呈現

在預做期間：

* 與使用者的瀏覽器之間的互動連接不存在。
* 瀏覽器還沒有可執行 JavaScript 程式碼的頁面。

`localStorage`或 `sessionStorage` 在預做期間無法使用。 如果元件嘗試與儲存體互動，則會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。

解決錯誤的其中一種方法是停用已處理的。 如果應用程式大量使用以瀏覽器為基礎的存放裝置，這通常是最佳的選擇。 因為應用程式無法提供任何有用的內容，直到 `localStorage` 或可供使用，因此會增加複雜性並不會讓應用程式受益 `sessionStorage` 。

若要停用預呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

如果是其他未使用或的頁面，則會進行預呈現 `localStorage` `sessionStorage` 。 若要保留已呈現，請延遲載入作業，直到瀏覽器連線到線路為止。 以下是儲存計數器值的範例：

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>將狀態保留分解為一般位置

如果許多元件都依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼很多次會建立程式碼重複。 避免程式碼重複的其中一個選項是建立一個可封裝狀態提供者邏輯的*狀態供應器父元件*。 子元件可以使用持續性資料，而不考慮狀態持續性機制。

在下列元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`元件在載入完成之前，不會呈現其子內容來處理載入階段。

若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。 若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

包裝的元件會接收並可修改保存的計數器狀態。 下列 `Counter` 元件會執行模式：

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

先前的元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。

如先前所述，若要處理已進行的已 `CounterStateProvider` 修訂，可以修改，讓取用計數器資料的所有元件都能自動以可處理方式使用。 如需詳細資訊，請參閱[處理預呈現](#handle-prerendering)一節。

一般情況下，建議使用*狀態供應器的父元件*模式：

* 跨許多元件取用狀態。
* 如果只有一個最上層狀態物件要保存，則為。

若要保存許多不同的狀態物件，並在不同位置使用不同的物件子集，最好避免全域保存狀態。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>受保護的瀏覽器儲存體實驗性 NuGet 套件

ASP.NET Core 受保護的瀏覽器存放裝置會利用與[ASP.NET Core 的資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`是不支援的實驗性封裝，不適用於生產用途。
>
> 套件僅適用于 ASP.NET Core 3.1 Blazor Server 應用程式。

### <a name="configuration"></a>組態

1. 將套件參考新增至 [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) 。
1. 在檔案中 `Pages/_Host.cshtml` ，于結束記號內新增下列腳本 `</body>` ：

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. 在中 `Startup.ConfigureServices` ，呼叫 `AddProtectedBrowserStorage` 以將 `localStorage` 和服務新增 `sessionStorage` 至服務集合：

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>儲存和載入元件中的資料

在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

選擇取決於您想要使用的瀏覽器儲存位置。 在下列範例中， `sessionStorage` 會使用：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`語句可以放在檔案中， `_Imports.razor` 而不是放置在元件中。 使用檔案 `_Imports.razor` 會讓應用程式或整個應用程式的較大區段可以使用命名空間。

若要根據 `currentCount` 專案範本，將值保存在 `Counter` 應用程式的元件中 Blazor Server ，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

在更大、更實際的應用程式中，個別欄位的儲存是不太可能發生的情況。 應用程式較可能儲存包含複雜狀態的整個模型物件。 `ProtectedSessionStore`自動序列化和還原序列化 JSON 資料。

在上述程式碼範例中， `currentCount` 資料會 `sessionStorage['count']` 在使用者的瀏覽器中儲存為。 資料不會以純文字儲存，而是使用 ASP.NET Core 資料保護來保護。 如果 `sessionStorage['count']` 是在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。

若要在 `currentCount` 使用者稍後回到元件時復原資料 `Counter` ，包括如果它們是在全新的線路上，請使用 `ProtectedSessionStore.GetAsync` ：

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

如果元件的參數包含導覽狀態，請呼叫 `ProtectedSessionStore.GetAsync` 並在中指派結果 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>只有在第一次具現化元件時，才會呼叫。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>如果使用者流覽至不同的 URL，但仍在相同頁面上，則不會再呼叫一次。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。

> [!WARNING]
> 此章節中的範例僅適用于伺服器未啟用預先安裝的情況。 啟用自動處理時，會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。
>
> 請停用已選擇的，或加入額外的程式碼以使用可處理的。 若要深入瞭解如何撰寫可搭配已預呈現運作的程式碼，請參閱[處理預呈現](#handle-prerendering)一節。

### <a name="handle-the-loading-state"></a>處理載入狀態

由於瀏覽器存放裝置是透過網路連接以非同步方式存取，因此在載入資料並提供給元件之前，一律會有一段時間。 若要獲得最佳結果，請在載入進行時轉譯載入狀態訊息，而不是顯示空白或預設的資料。

其中一種方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。 在預設 `Counter` 元件中，計數會保留在中 `int` 。 將問號 () 加入 () 的類型，[使其成為 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：

```csharp
private int? currentCount;
```

不是無條件顯示計數和 **`Increment`** 按鈕，而是只在載入資料時，選擇顯示這些元素：

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>處理已預呈現

在預做期間：

* 與使用者的瀏覽器之間的互動連接不存在。
* 瀏覽器還沒有可執行 JavaScript 程式碼的頁面。

`localStorage`或 `sessionStorage` 在預做期間無法使用。 如果元件嘗試與儲存體互動，則會產生錯誤，說明無法發出 JavaScript interop 呼叫，因為元件正在資源清單中。

解決錯誤的其中一種方法是停用已處理的。 如果應用程式大量使用以瀏覽器為基礎的存放裝置，這通常是最佳的選擇。 因為應用程式無法提供任何有用的內容，直到 `localStorage` 或可供使用，因此會增加複雜性並不會讓應用程式受益 `sessionStorage` 。

若要停用預呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

如果是其他未使用或的頁面，則會進行預呈現 `localStorage` `sessionStorage` 。 若要保留已呈現，請延遲載入作業，直到瀏覽器連線到線路為止。 以下是儲存計數器值的範例：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>將狀態保留分解為一般位置

如果許多元件都依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼很多次會建立程式碼重複。 避免程式碼重複的其中一個選項是建立一個可封裝狀態提供者邏輯的*狀態供應器父元件*。 子元件可以使用持續性資料，而不考慮狀態持續性機制。

在下列元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`元件在載入完成之前，不會呈現其子內容來處理載入階段。

若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。 若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

包裝的元件會接收並可修改保存的計數器狀態。 下列 `Counter` 元件會執行模式：

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

先前的元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。

如先前所述，若要處理已進行的已 `CounterStateProvider` 修訂，可以修改，讓取用計數器資料的所有元件都能自動以可處理方式使用。 如需詳細資訊，請參閱[處理預呈現](#handle-prerendering)一節。

一般情況下，建議使用*狀態供應器父元件*模式：

* 跨許多元件取用狀態。
* 如果只有一個最上層狀態物件要保存，則為。

若要保存許多不同的狀態物件，並在不同位置使用不同的物件子集，最好避免全域保存狀態。

::: moniker-end

::: zone-end
