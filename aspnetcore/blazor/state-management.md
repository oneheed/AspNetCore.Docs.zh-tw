---
title: ASP.NET 核心 Blazor 狀態管理
author: guardrex
description: 瞭解如何在應用程式中保存狀態 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: fee4473aede3fd213314c51a18284b4e4ad95b2b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394664"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET 核心 Blazor 狀態管理

::: zone pivot="webassembly"

在應用程式中建立的使用者狀態 Blazor WebAssembly 會保留在瀏覽器的記憶體中。

在瀏覽器記憶體中保留的使用者狀態範例包括：

* 轉譯的 UI 中元件實例及其最新轉譯輸出的階層。
* 元件實例中欄位和屬性的值。
* 保存在相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務實例中的資料。
* 透過 [JavaScript interop](xref:blazor/call-javascript-from-dotnet) 呼叫設定的值。

當使用者關閉並重新開啟其瀏覽器或重載網頁時，瀏覽器記憶體中保留的使用者狀態將會遺失。

> [!NOTE]
> [受保護的瀏覽器儲存體](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> 命名空間) 依賴 ASP.NET 核心資料保護，且僅支援 Blazor Server 應用程式。

## <a name="persist-state-across-browser-sessions"></a>跨瀏覽器會話保存狀態

一般而言，在使用者主動建立資料的瀏覽器會話中維護狀態，而不只是讀取已經存在的資料。

若要在瀏覽器會話之間保持狀態，應用程式必須將資料保存到其他儲存位置，而不是瀏覽器的記憶體。 狀態持續性不是自動的。 您必須在開發應用程式時採取步驟，以執行可設定狀態的資料持續性。

通常只有在使用者需要花費更多時間來建立的高價值狀態下，才需要資料持續性。 在下列範例中，保存狀態可以節省商務工作的時間或輔助：

* 多重步驟的 web 表單：當使用者的狀態遺失時，使用者重新輸入多步驟 web 表單的幾個已完成步驟的資料相當耗時。 如果使用者離開表單並在稍後返回，則會在此案例中失去狀態。
* 購物車：應用程式中任何代表潛在收益的商業重要元件都可以維護。 如果使用者遺失其狀態，而使用者的購物車可能會在稍後返回網站時購買較少的產品或服務。

應用程式只能保存 *應用程式狀態*。 Ui 無法保存，例如元件實例和其轉譯樹狀結構。 元件和轉譯樹狀結構通常不是可序列化的。 若要保存 UI 狀態，例如樹狀檢視控制項的展開節點，應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。

## <a name="where-to-persist-state"></a>保存狀態的位置

存在於保存狀態的常見位置：

* [伺服器端儲存空間](#server-side-storage-wasm)
* [URL](#url-wasm)
* [瀏覽器儲存體](#browser-storage-wasm)
* [記憶體內部狀態容器服務](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm">伺服器端儲存空間</h2>

針對跨越多個使用者和裝置的永久資料持續性，應用程式可以使用透過 web API 存取的獨立伺服器端儲存體。 這些選項包括：

* Blob 儲存體
* 機碼值儲存體
* 關聯式資料庫
* 資料表儲存體

儲存資料之後，會保留使用者的狀態，並可在任何新的瀏覽器會話中使用。

由於 Blazor WebAssembly 應用程式完全是在使用者的瀏覽器中執行，因此需要額外的措施來存取安全的外部系統，例如儲存體服務和資料庫。 Blazor WebAssembly 應用程式的保護方式與單一頁面應用程式 (Spa) 相同。 一般而言，應用程式會透過[OAuth](https://oauth.net) / [OpenID connect (OIDC) ](https://openid.net/connect/)驗證使用者，然後透過 web API 呼叫，與伺服器端應用程式的儲存體服務和資料庫互動。 伺服器端應用程式會協調 Blazor WebAssembly 應用程式與儲存體服務或資料庫之間的資料傳輸。 Blazor WebAssembly應用程式會維護與伺服器端應用程式的暫時連線，而伺服器端應用程式會持續連線至儲存體。

如需詳細資訊，請參閱下列資源：

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*安全性和 Identity* 文章

如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：

* [Azure 資料庫](https://azure.microsoft.com/product-categories/databases/)
* [Azure 儲存體檔](/azure/storage/)

<h2 id="url-wasm">URL</h2>

若為代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。 在 URL 中模型化使用者狀態的範例包括：

* 已查看之實體的識別碼。
* 分頁方格中的目前頁碼。

如果使用者以手動方式重載網頁，則會保留瀏覽器網址列的內容。

如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。

<h2 id="browser-storage-wasm">瀏覽器儲存體</h2>

對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：

* `localStorage` 範圍設定為瀏覽器的視窗。 如果使用者重載頁面或關閉並重新開啟瀏覽器，則狀態會持續存在。 如果使用者開啟多個瀏覽器索引標籤，則會在索引標籤之間共用狀態。 資料會持續存在， `localStorage` 直到明確清除為止。
* `sessionStorage` 的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載此索引標籤，則狀態會持續存在。 如果使用者關閉索引標籤或瀏覽器，狀態就會遺失。 如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。

> [!NOTE]
> `localStorage` 和 `sessionStorage` 可以在應用程式中使用， Blazor WebAssembly 但只能透過撰寫自訂程式碼或使用協力廠商套件。

一般而言， `sessionStorage` 使用會比較安全。 `sessionStorage` 避免使用者開啟多個索引標籤，並遇到下列情況的風險：

* 所有索引標籤的狀態儲存區中的 bug。
* 當索引標籤覆寫其他索引標籤的狀態時，有混淆的行為。

`localStorage` 如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，則這是較佳的選擇。

> [!WARNING]
> 使用者可能會看到或篡改儲存在和中的資料 `localStorage` `sessionStorage` 。

<h2 id="in-memory-state-container-service-wasm">記憶體內部狀態容器服務</h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a>其他資源

* [在驗證操作之前儲存應用程式狀態](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server 是具狀態的應用程式架構。 大部分的情況下，應用程式會維護與伺服器的連線。 使用者的狀態會保留在 *伺服器的記憶體中。* 

線路中保留的使用者狀態範例包括：

* 轉譯的 UI 中元件實例及其最新轉譯輸出的階層。
* 元件實例中欄位和屬性的值。
* 保存在相依性插入中的資料 [ (DI) ](xref:fundamentals/dependency-injection) 範圍設定為線路的服務實例。

您也可以透過 [javascript interop](xref:blazor/call-javascript-from-dotnet) 呼叫，在瀏覽器記憶體集中的 javascript 變數中找到使用者狀態。

如果使用者遇到暫時性的網路連接遺失， Blazor 會嘗試將使用者重新連接到其原始狀態的原始線路。 不過，不一定能夠將使用者重新連接到其在伺服器記憶體中的原始線路：

* 伺服器無法永遠保留中斷連線的電路。 伺服器必須在超時之後或伺服器處於記憶體不足的壓力之後釋放中斷連接的線路。
* 在多伺服器的負載平衡部署環境中，個別的伺服器可能會失敗，或在不再需要處理要求的整體數量時自動移除。 當使用者嘗試重新連接時，使用者的原始伺服器處理要求可能會變成無法使用。
* 使用者可能會關閉並重新開啟其瀏覽器或重載頁面，以移除瀏覽器記憶體中保留的任何狀態。 例如，透過 JavaScript interop 呼叫設定的 JavaScript 變數值會遺失。

當使用者無法重新連接到其原始線路時，使用者會收到具有空白狀態的新電路。 這相當於關閉並重新開啟桌面應用程式。

## <a name="persist-state-across-circuits"></a>跨線路保存狀態

一般來說，在使用者主動建立資料的線路上維護狀態，而不只是讀取已經存在的資料。

若要保留跨電路的狀態，應用程式必須將資料保存到與伺服器記憶體不同的儲存位置。 狀態持續性不是自動的。 您必須在開發應用程式時採取步驟，以執行可設定狀態的資料持續性。

通常只有在使用者需要花費更多時間來建立的高價值狀態下，才需要資料持續性。 在下列範例中，保存狀態可以節省商務工作的時間或輔助：

* 多重步驟的 web 表單：當使用者的狀態遺失時，使用者重新輸入多步驟 web 表單的幾個已完成步驟的資料相當耗時。 如果使用者離開表單並在稍後返回，則會在此案例中失去狀態。
* 購物車：應用程式中任何代表潛在收益的商業重要元件都可以維護。 如果使用者遺失其狀態，而使用者的購物車可能會在稍後返回網站時購買較少的產品或服務。

應用程式只能保存 *應用程式狀態*。 Ui 無法保存，例如元件實例和其轉譯樹狀結構。 元件和轉譯樹狀結構通常不是可序列化的。 若要保存 UI 狀態，例如樹狀檢視控制項的展開節點，應用程式必須使用自訂程式碼，將 UI 狀態的行為模型化為可序列化的應用程式狀態。

## <a name="where-to-persist-state"></a>保存狀態的位置

存在於保存狀態的常見位置：

* [伺服器端儲存空間](#server-side-storage-server)
* [URL](#url-server)
* [瀏覽器儲存體](#browser-storage-server)
* [記憶體內部狀態容器服務](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server">伺服器端儲存空間</h2>

針對跨越多個使用者和裝置的永久資料持續性，應用程式可以使用伺服器端儲存體。 這些選項包括：

* Blob 儲存體
* 機碼值儲存體
* 關聯式資料庫
* 資料表儲存體

儲存資料之後，會保留使用者的狀態，並可在任何新的線路中使用。

如需 Azure 資料儲存體選項的詳細資訊，請參閱下列各項：

* [Azure 資料庫](https://azure.microsoft.com/product-categories/databases/)
* [Azure 儲存體檔](/azure/storage/)

<h2 id="url-server">URL</h2>

若為代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。 在 URL 中模型化使用者狀態的範例包括：

* 已查看之實體的識別碼。
* 分頁方格中的目前頁碼。

瀏覽器網址列的內容會保留：

* 如果使用者手動重載頁面。
* 如果網頁伺服器變得無法使用，則會強制使用者重載頁面，以便連接到不同的伺服器。

如需使用指示詞定義 URL 模式的詳細資訊 [`@page`](xref:mvc/views/razor#page) ，請參閱 <xref:blazor/fundamentals/routing> 。

<h2 id="browser-storage-server">瀏覽器儲存體</h2>

對於使用者主動建立的暫時性資料，常用的儲存位置是瀏覽器 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：

* `localStorage` 範圍設定為瀏覽器的視窗。 如果使用者重載頁面或關閉並重新開啟瀏覽器，則狀態會持續存在。 如果使用者開啟多個瀏覽器索引標籤，則會在索引標籤之間共用狀態。 資料會持續存在， `localStorage` 直到明確清除為止。
* `sessionStorage` 的範圍設定為 [瀏覽器] 索引標籤。如果使用者重載此索引標籤，則狀態會持續存在。 如果使用者關閉索引標籤或瀏覽器，狀態就會遺失。 如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。

一般而言， `sessionStorage` 使用會比較安全。 `sessionStorage` 避免使用者開啟多個索引標籤，並遇到下列情況的風險：

* 所有索引標籤的狀態儲存區中的 bug。
* 當索引標籤覆寫其他索引標籤的狀態時，有混淆的行為。

`localStorage` 如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，則這是較佳的選擇。

使用瀏覽器存放裝置的注意事項：

* 與使用伺服器端資料庫類似，載入和儲存資料是非同步。
* 與伺服器端資料庫不同的是，在預先呈現期間，儲存體無法使用，因為在預先設定的階段中，要求的頁面不存在於瀏覽器中。
* 儲存幾 kb 的資料可合理保存 Blazor Server 應用程式。 除了幾 kb 以外，您還必須考慮效能影響，因為資料是在網路上載入和儲存。
* 使用者可能會看到或篡改資料。 [ASP.NET Core 資料保護](xref:security/data-protection/introduction) 可減輕風險。 例如， [ASP.NET Core 受保護的瀏覽器儲存體](#aspnet-core-protected-browser-storage) 使用 ASP.NET Core 資料保護。

協力廠商 NuGet 套件提供使用和的 Api `localStorage` `sessionStorage` 。 值得考慮選擇明確使用 [ASP.NET Core 資料保護](xref:security/data-protection/introduction)的套件。 資料保護會加密儲存的資料，並減少篡改儲存資料的潛在風險。 如果以純文字儲存 JSON 序列化資料，使用者就可以使用瀏覽器開發人員工具來查看資料，也可以修改儲存的資料。 保護資料並不一定會造成問題，因為資料在本質上可能很簡單。 例如，讀取或修改 UI 元素的預存色彩，對於使用者或組織而言並不是重大的安全性風險。 避免讓使用者檢查或篡改 *機密資料*。

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET 核心受保護的瀏覽器儲存體

ASP.NET 核心受保護的瀏覽器儲存體可利用和的 [ASP.NET Core 資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。

> [!NOTE]
> 受保護的瀏覽器存放裝置依賴 ASP.NET 核心資料保護，且僅支援 Blazor Server 應用程式。

### <a name="save-and-load-data-within-a-component"></a>儲存並載入元件中的資料

在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，請使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

選擇取決於您想要使用的瀏覽器儲存位置。 在下列範例中， `sessionStorage` 會使用：

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

指示詞 `@using` 可以放在應用程式的檔案中，而不是放在 `_Imports.razor` 元件中。 使用檔案可 `_Imports.razor` 讓應用程式的較大區段或整個應用程式使用命名空間。

若要在以 `currentCount` `Counter` [ Blazor Server 專案範本](xref:blazor/project-structure)為基礎的應用程式元件中保存值，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

在更大、更實際的應用程式中，個別欄位的儲存是不太可能的情況。 應用程式更有可能儲存包含複雜狀態的整個模型物件。 `ProtectedSessionStore` 自動序列化和還原序列化 JSON 資料，以儲存複雜的狀態物件。

在上述程式碼範例中， `currentCount` 資料會儲存 `sessionStorage['count']` 在使用者的瀏覽器中。 資料不會以純文字儲存，而是使用 ASP.NET 核心資料保護來保護。 如果 `sessionStorage['count']` 在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。

若要在 `currentCount` 使用者稍後返回元件時復原資料 `Counter` ，包括使用者是否在新線路上，請使用 `ProtectedSessionStore.GetAsync` ：

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

如果元件的參數包含流覽狀態，請呼叫 `ProtectedSessionStore.GetAsync` 並指派非 `null` 結果中的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 只有在第一次具現化元件時，才會呼叫一次。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 如果使用者流覽至不同的 URL，並在相同頁面上剩餘，則不會再次呼叫。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。

> [!WARNING]
> 此區段中的範例只適用于伺服器未啟用可呈現的情況。 啟用自動呈現之後，會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。
>
> 停用已呈現或新增額外的程式碼，以使用可呈現的。 若要深入瞭解如何撰寫可搭配可進行處理的程式碼，請參閱處理常式性的 [ [處理](#handle-prerendering) 程式] 區段

### <a name="handle-the-loading-state"></a>處理載入狀態

由於瀏覽器儲存體會透過網路連線以非同步方式存取，因此在載入資料並可供元件使用之前，一律會有一段時間。 為了獲得最佳結果，在載入過程中轉譯載入狀態訊息，而不是顯示空白或預設的資料。

其中一個方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。 在預設 `Counter` 元件中，計數會保留在中 `int` 。 藉由將問號 () 新增至類型 () 來[建立 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：

```csharp
private int? currentCount;
```

**`Increment`** 只有在透過檢查來載入資料時，才會顯示這些元素，而不是無條件地顯示計數和按鈕 <xref:System.Nullable%601.HasValue%2A> ：

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

### <a name="handle-prerendering"></a>處理已呈現

在呈現期間：

* 與使用者瀏覽器的互動式連接不存在。
* 瀏覽器還沒有可執行 JavaScript 程式碼的頁面。

`localStorage` 或 `sessionStorage` 無法在預進行期間使用。 如果元件嘗試與儲存體互動，就會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。

解決此錯誤的其中一種方式是停用已呈現的。 如果應用程式大量使用以瀏覽器為基礎的儲存體，這通常是最佳選擇。 由於應用程式無法取得任何有用的內容，直到 `localStorage` 或可供使用，因此會導致應用程式不會讓應用程式受益 `sessionStorage` 。

若要停用已呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

針對不使用或的其他頁面，可進行的自動呈現可能很有用 `localStorage` `sessionStorage` 。 若要保留已呈現，請延遲載入作業，直到瀏覽器連線至線路為止。 以下是儲存計數器值的範例：

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>將狀態保留的狀態考慮到一般位置

如果有許多元件依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼多次會建立程式碼重複。 避免程式碼重複的其中一個選項，就是建立可封裝狀態提供者邏輯的 *狀態提供者父元件* 。 子元件可以使用保存的資料，而不需要考慮狀態持續性機制。

在下列的元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
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

元件在載入 `CounterStateProvider` 完成之前，不會轉譯其子內容來處理載入階段。

若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。 若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

包裝的元件會接收和修改持續性的計數器狀態。 下列元件會實 `Counter` 作為模式：

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

上述元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。

如先前所述，若要處理如先前所述的自動完成， `CounterStateProvider` 可以修改所有使用計數器資料的元件，以便自動進行可呈現。 如需詳細資訊，請參閱處理常式的已進行 [處理](#handle-prerendering) 一節。

一般情況下，建議使用「 *狀態供應器」父元件* 模式：

* 跨多個元件使用狀態。
* 如果只保留一個最上層狀態物件。

若要保存許多不同的狀態物件，並在不同位置取用不同的物件子集，最好避免全域保存狀態。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>受保護的瀏覽器儲存體實驗性 NuGet 套件

ASP.NET 核心受保護的瀏覽器儲存體可利用和的 [ASP.NET Core 資料保護](xref:security/data-protection/introduction) [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 。

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` 是不受支援的實驗性套件，不適用於生產用途。
>
> 封裝僅適用于 ASP.NET Core 3.1 Blazor Server 應用程式。

### <a name="configuration"></a>組態

1. 將封裝參考加入至 [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) 。
1. 在檔案中 `Pages/_Host.cshtml` ，將下列腳本新增至結尾 `</body>` 標記內：

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. 在中 `Startup.ConfigureServices` ，呼叫 `AddProtectedBrowserStorage` 以將 `localStorage` 和服務加入 `sessionStorage` 至服務集合：

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>儲存並載入元件中的資料

在需要將資料載入或儲存至瀏覽器儲存體的任何元件中，請使用指示詞 [`@inject`](xref:mvc/views/razor#inject) 插入下列任一項的實例：

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

選擇取決於您想要使用的瀏覽器儲存位置。 在下列範例中， `sessionStorage` 會使用：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`語句可以放入檔案中， `_Imports.razor` 而不是放在元件中。 使用檔案可 `_Imports.razor` 讓應用程式的較大區段或整個應用程式使用命名空間。

若要在以 `currentCount` `Counter` [ Blazor Server 專案範本](xref:blazor/project-structure)為基礎的應用程式元件中保存值，請修改 `IncrementCount` 要使用的方法 `ProtectedSessionStore.SetAsync` ：

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

在更大、更實際的應用程式中，個別欄位的儲存是不太可能的情況。 應用程式更有可能儲存包含複雜狀態的整個模型物件。 `ProtectedSessionStore` 自動序列化和還原序列化 JSON 資料。

在上述程式碼範例中， `currentCount` 資料會儲存 `sessionStorage['count']` 在使用者的瀏覽器中。 資料不會以純文字儲存，而是使用 ASP.NET 核心資料保護來保護。 如果 `sessionStorage['count']` 在瀏覽器的開發人員主控台中評估，則可以檢查加密的資料。

若要在 `currentCount` 使用者稍後返回元件時復原資料 `Counter` ，包括是否在全新的線路上，請使用 `ProtectedSessionStore.GetAsync` ：

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

如果元件的參數包含導覽狀態，請呼叫 `ProtectedSessionStore.GetAsync` 並指派中的結果 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 只有在第一次具現化元件時，才會呼叫一次。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 如果使用者流覽至不同的 URL，並在相同頁面上剩餘，則不會再次呼叫。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。

> [!WARNING]
> 此區段中的範例只適用于伺服器未啟用可呈現的情況。 啟用自動呈現之後，會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。
>
> 停用已呈現或新增額外的程式碼，以使用可呈現的。 若要深入瞭解如何撰寫可搭配可進行處理的程式碼，請參閱處理常式性的 [ [處理](#handle-prerendering) 程式] 區段

### <a name="handle-the-loading-state"></a>處理載入狀態

由於瀏覽器儲存體會透過網路連線以非同步方式存取，因此在載入資料並可供元件使用之前，一律會有一段時間。 為了獲得最佳結果，在載入過程中轉譯載入狀態訊息，而不是顯示空白或預設的資料。

其中一個方法是追蹤資料是否為 `null` ，這表示資料仍在載入中。 在預設 `Counter` 元件中，計數會保留在中 `int` 。 藉由將問號 () 新增至類型 () 來[建立 `currentCount` 可為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` ：

```csharp
private int? currentCount;
```

**`Increment`** 只有在載入資料時，才選擇顯示這些元素，而不是無條件地顯示計數和按鈕：

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

### <a name="handle-prerendering"></a>處理已呈現

在呈現期間：

* 與使用者瀏覽器的互動式連接不存在。
* 瀏覽器還沒有可執行 JavaScript 程式碼的頁面。

`localStorage` 或 `sessionStorage` 無法在預進行期間使用。 如果元件嘗試與儲存體互動，就會產生錯誤，說明因為正在資源清單元件，所以無法發出 JavaScript interop 呼叫。

解決此錯誤的其中一種方式是停用已呈現的。 如果應用程式大量使用以瀏覽器為基礎的儲存體，這通常是最佳選擇。 由於應用程式無法取得任何有用的內容，直到 `localStorage` 或可供使用，因此會導致應用程式不會讓應用程式受益 `sessionStorage` 。

若要停用已呈現，請開啟檔案， `Pages/_Host.cshtml` 然後將 `render-mode` [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式的屬性變更為 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> ：

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

針對不使用或的其他頁面，可進行的自動呈現可能很有用 `localStorage` `sessionStorage` 。 若要保留已呈現，請延遲載入作業，直到瀏覽器連線至線路為止。 以下是儲存計數器值的範例：

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>將狀態保留的狀態考慮到一般位置

如果有許多元件依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼多次會建立程式碼重複。 避免程式碼重複的其中一個選項，就是建立可封裝狀態提供者邏輯的 *狀態提供者父元件* 。 子元件可以使用保存的資料，而不需要考慮狀態持續性機制。

在下列的元件範例中 `CounterStateProvider` ，計數器資料會保存到 `sessionStorage` ：

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

元件在載入 `CounterStateProvider` 完成之前，不會轉譯其子內容來處理載入階段。

若要使用 `CounterStateProvider` 元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。 若要讓應用程式中的所有元件都能存取該狀態，請將 `CounterStateProvider` 元件包裝 <xref:Microsoft.AspNetCore.Components.Routing.Router> 在 `App` 元件 (`App.razor`) 中：

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

包裝的元件會接收和修改持續性的計數器狀態。 下列元件會實 `Counter` 作為模式：

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

上述元件不需要與互動 `ProtectedBrowserStorage` ，也不會處理「載入」階段。

如先前所述，若要處理如先前所述的自動完成， `CounterStateProvider` 可以修改所有使用計數器資料的元件，以便自動進行可呈現。 如需詳細資訊，請參閱處理常式的已進行 [處理](#handle-prerendering) 一節。

一般情況下，建議使用「 *狀態供應器」父元件* 模式：

* 跨多個元件使用狀態。
* 如果只保留一個最上層狀態物件。

若要保存許多不同的狀態物件，並在不同位置取用不同的物件子集，最好避免全域保存狀態。

::: moniker-end

<h2 id="in-memory-state-container-service-server">記憶體內部狀態容器服務</h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
