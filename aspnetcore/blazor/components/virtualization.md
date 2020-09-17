---
title: ASP.NET Core Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/16/2020
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
ms.openlocfilehash: c904a3f330531f80d1dc69bf63621c59790e1cb0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722975"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor 元件虛擬化

依 [Daniel Roth](https://github.com/danroth27)

使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。 虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。 例如，當應用程式必須轉譯長清單或具有許多資料列的資料表，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。 Blazor 提供 `Virtualize` 可用於將虛擬化新增至應用程式元件的元件。

::: moniker range=">= aspnetcore-5.0"

如果沒有虛擬化，一般清單或資料表元件可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案，或資料表中的每個資料列：

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

如果清單包含上千個專案，則轉譯清單可能需要很長的時間。 使用者可能會遇到明顯的 UI 延遲。

請將迴圈取代為 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` 元件，並使用指定固定專案來源，而不是一次轉譯清單中的每個專案 `Items` 。 只有目前可見的專案會呈現：

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

如果未使用來指定元件的 `Context` 內容，請使用 `context` `@context.{PROPERTY}` 專案內容範本中 () 值：

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

`Virtualize`元件會根據容器的高度和轉譯專案的大小，計算要轉譯的專案數目。

## <a name="item-provider-delegate"></a>專案提供者委派

如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 `ItemsProvider` 參數，以根據需求非同步地抓取要求的專案：

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
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
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a>項目大小

您可以使用 (預設值來設定每個專案的大小（以圖元為單位）： `ItemSize` 50px) ：

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a>Overscan 計數

`OverscanCount` 決定在可見區域之前和之後轉譯的額外專案數目。 這項設定有助於減少滾動期間轉譯的頻率。 不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) ：

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

例如，呈現數百個包含元件之資料列的方格或清單，需要處理器密集的轉譯。 請考慮將方格或清單版面配置虛擬化，如此一來，就只會在任何指定時間轉譯元件的子集。 如需元件子集轉譯的範例，請參閱[ `Virtualization` (aspnet/samples GitHub 存放庫) 範例應用程式](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)中的下列元件：

* `Virtualize` 元件 ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)) ：以 c # 撰寫的元件，它會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 根據使用者的滾動方式來呈現一組氣象資料列。
* `FetchData` 元件 ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)) ：使用 `Virtualize` 元件一次顯示25個數據列的氣象資料。

::: moniker-end
