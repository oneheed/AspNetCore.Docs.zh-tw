---
title: ASP.NET Core Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
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
ms.openlocfilehash: eafad420d72a974cc64ebfd6abb3eff2d73a115d
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805553"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor 元件虛擬化

依 [Daniel Roth](https://github.com/danroth27)

使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。 虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。 例如，當應用程式必須轉譯長清單的專案，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。 Blazor 提供 `Virtualize` 可用於將虛擬化新增至應用程式元件的元件。

如果沒有虛擬化，一般清單可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案：

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

如果清單包含上千個專案，則轉譯清單可能需要很長的時間。 使用者可能會遇到明顯的 UI 延遲。

請將迴圈取代為 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` 元件，並使用指定固定專案來源，而不是一次轉譯清單中的每個專案 `Items` 。 只有目前可見的專案會呈現：

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

如果未使用來指定元件的 `Context` 內容，請使用 `context` `@context.{PROPERTY}` 專案內容範本中 () 值：

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

`Virtualize`元件會根據容器的高度和轉譯專案的大小，計算要轉譯的專案數目。

元件的專案內容 `Virtualize` 可以包括：

* Razor如同上述範例所示的純 HTML 和程式碼。
* 一或多個 Razor 元件。
* HTML/ Razor 和元件的混合 Razor 。

## <a name="item-provider-delegate"></a>專案提供者委派

如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 `ItemsProvider` 參數，以根據需求非同步地抓取要求的專案：

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

專案提供者會接收 `ItemsProviderRequest` ，以指定從特定開始索引開始所需的專案數目。 然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 `ItemsProviderResult<TItem>` 連同總專案數一起傳回。 專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。 請勿嘗試使用專案提供者，並將集合指派給 `Items` 相同的 `Virtualize` 元件。

下列範例會從載入員工 `EmployeeService` ：

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

## <a name="placeholder"></a>預留位置

由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇 () 呈現預留位置， `<Placeholder>...</Placeholder>` 直到專案資料可用為止：

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

您可以使用 (預設值來設定每個專案的大小（以圖元為單位）： `ItemSize` 50px) ：

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Overscan 計數

`OverscanCount` 決定在可見區域之前和之後轉譯的額外專案數目。 這項設定有助於減少滾動期間轉譯的頻率。 不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) ：

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>狀態變更

對元件所轉譯的專案進行變更時 `Virtualize` ，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以強制重新評估和轉譯資料流程元件。
