---
title: ASP.NET 核心 Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2021
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
uid: blazor/components/virtualization
ms.openlocfilehash: c81732c29b262e9134a4ff7dab077a4f31db96af
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109815"
---
# <a name="aspnet-core-blazor-component-virtualization"></a>ASP.NET 核心 Blazor 元件虛擬化

使用此 Blazor [ `Virtualize` 元件](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)的架構內建虛擬化支援，改善元件轉譯的認知效能。 虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。 例如，當應用程式必須轉譯長清單的專案，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。

使用元件的時機 `Virtualize` ：

* 在迴圈中呈現一組資料項目。
* 大部分的專案都因為滾動而無法顯示。
* 轉譯的專案大小相同。

當使用者滾動至元件的專案清單中的任意點時 `Virtualize` ，該元件會計算要顯示的可見專案。 未顯示的專案則不會呈現。

如果沒有虛擬化，一般清單可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案。 在下例中︰

* `allFlights` 是飛機航班的集合。
* `FlightSummary`元件會顯示每個航班的詳細資料。
* 指示詞[ `@key` 屬性](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)會將每個元件的關聯性保留 `FlightSummary` 給航班的轉譯飛行 `FlightId` 。

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

如果集合包含上千個航班，則轉譯航班需要很長的時間，而且使用者會遇到明顯的 UI 延遲。 大部分航班都不會轉譯，因為它們落在元素的高度之外 `<div>` 。

請將上述範例中的迴圈取代為元件，而不是一次轉譯整個航班清單 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` ：

* `allFlights`將指定為的固定專案來源 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> 。 只有目前可見的航班會由元件呈現 `Virtualize` 。
* 使用參數指定每個航班的內容 `Context` 。 在下列範例中， `flight` 會使用做為內容，以提供每個航班成員的存取權。

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

如果未使用參數來指定內容 `Context` ，請使用 `context` 專案內容範本中的值來存取每個航班的成員：

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

`Virtualize`元件：

* 根據容器的高度和轉譯專案的大小，計算要呈現的專案數目。
* 在使用者滾動時重新計算和轉譯中專案。
* 只會從對應到目前可見區域的外部 API 提取記錄的配量，而不是從集合中下載所有資料。

元件的專案內容 `Virtualize` 可以包括：

* Razor如同上述範例所示的純 HTML 和程式碼。
* 一或多個 Razor 元件。
* HTML/ Razor 和元件的混合 Razor 。

## <a name="item-provider-delegate"></a>專案提供者委派

如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 參數，以根據需求非同步地抓取要求的專案。 在下列範例中，方法會將 `LoadEmployees` 專案提供給 `Virtualize` 元件：

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

專案提供者會接收 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> ，以指定從特定開始索引開始所需的專案數目。 然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> 連同總專案數一起傳回。 專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。

`Virtualize`元件只能接受 **一個專案來源** 的參數，因此請勿嘗試同時使用專案提供者，並將集合指派給 `Items` 。 如果兩者都被指派， <xref:System.InvalidOperationException> 當元件的參數在執行時間設定時，就會擲回。

下列 `LoadEmployees` 方法範例 `EmployeeService` 會從未顯示)  (載入員工：

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> 指示元件從其 rerequest 資料 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> 。 當外部資料變更時，這會很有用。 使用時，不需要呼叫 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> 。

## <a name="placeholder"></a>預留位置

由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇以專案內容呈現預留位置：

* 使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) 來顯示內容，直到專案資料可供使用為止。
* 用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> 來設定清單的專案範本。

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>項目大小

每個專案的高度（以圖元為單位）可以使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (預設值： 50) 來設定。 下列範例會將每個專案的高度從預設的50圖元變更為25圖元：

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

根據預設，此 `Virtualize` 元件會測量轉譯大小 (在初始轉譯 *之後* ，個別專案的高度) 。 您 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 可以使用，事先提供確切的專案大小來協助精確的初始轉譯效能，以及確保頁面重載的正確滾動位置。 如果預設值 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 導致某些專案在目前可見的視圖之外轉譯，則會觸發第二次重呈現。 若要在虛擬化清單中正確維護瀏覽器的滾動位置，初始轉譯必須是正確的。 如果不是，使用者可能會看到錯誤的專案。

## <a name="overscan-count"></a>Overscan 計數

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> 決定在可見區域之前和之後轉譯的額外專案數目。 這項設定有助於減少滾動期間轉譯的頻率。 不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) 。 下列範例會將 overscan 計數從預設的三個專案變更為四個專案：

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>狀態變更

對元件所轉譯的專案進行變更時 `Virtualize` ，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以強制重新評估和轉譯資料流程元件。 如需詳細資訊，請參閱<xref:blazor/components/rendering>。
