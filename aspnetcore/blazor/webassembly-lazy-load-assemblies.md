---
title: ASP.NET Core 中的延遲載入元件Blazor WebAssembly
author: guardrex
description: 探索如何在 ASP.NET Core 應用程式中延遲載入元件 Blazor WebAssembly 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0fb744b4e9d44e6b8136123fddfb75ace8901d52
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819939"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core 中的延遲載入元件Blazor WebAssembly

By [Safia Abdalla](https://safia.rocks)和[Luke Latham](https://github.com/guardrex)

Blazor WebAssembly應用程式啟動效能可以藉由延後載入一些應用程式元件來改善，直到需要它們為止，這稱為「*延遲載入*」。 例如，只有在使用者流覽至該元件時，才會將用來轉譯單一元件的元件設定為僅載入。 載入之後，元件會快取用戶端，並可供所有未來的導覽使用。

Blazor的「消極式載入」功能可讓您將應用程式元件標示為消極式載入，這會在使用者流覽至特定路由時，于執行時間載入元件。 此功能包含專案檔的變更，以及應用程式路由器的變更。

> [!NOTE]
> 元件消極式載入不會讓應用程式受益 Blazor Server ，因為元件不會下載至應用程式中的用戶端 Blazor Server 。

## <a name="project-file"></a>專案檔

在應用程式的專案檔中，將延遲載入的元件標示 (`.csproj` 使用 `BlazorWebAssemblyLazyLoad` 專案) 。 請使用不含副檔名的元件名稱 `.dll` 。 Blazor架構會防止這個專案群組所指定的元件在應用程式啟動時載入。 下列範例會將大型自訂群組件標示 `GrantImaharaRobotControls.dll` 為延遲載入 () 。 如果標示為消極式載入的元件有相依性，則也必須在專案檔中將它們標示為延遲載入。

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

只有應用程式所使用的元件可以延遲載入。 連結器會從已發行的輸出中去除未使用的元件。

## <a name="router-component"></a>`Router` 元件

Blazor的 `Router` 元件會指定哪些元件會 Blazor 搜尋可路由的元件。 `Router`元件也會負責呈現使用者導覽之路由的元件。 `Router`元件支援 `OnNavigateAsync` 可與消極式載入搭配使用的功能。

在應用程式的 `Router` 元件 (`App.razor`) ：

* 新增 `OnNavigateAsync` 回呼。 `OnNavigateAsync`當使用者執行下列動作時，就會叫用處理程式：
  * 第一次造訪路線，方法是直接從他們的瀏覽器流覽至。
  * 使用連結或調用，流覽至新的路由 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。
* 如果消極式載入的元件包含可路由的元件，請將名為) 的[清單](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (例如，新增 `lazyLoadedAssemblies` 至元件。 當元件包含可路由的元件時，元件會傳回至 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 集合。 此架構會搜尋元件中的路由，並在找到任何新路由時更新路由集合。

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

如果 `OnNavigateAsync` 回呼擲回未處理的例外狀況，則會叫用[ Blazor 錯誤 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) 。

### <a name="assembly-load-logic-in-onnavigateasync"></a>中的元件載入邏輯`OnNavigateAsync`

`OnNavigateAsync`具有 `NavigationContext` 參數，可提供目前非同步導覽事件的相關資訊，包括目標路徑 (`Path`) 和取消權杖 (`CancellationToken`) ：

* `Path`屬性是相對於應用程式基底路徑的使用者目的地路徑，例如 `/robot` 。
* `CancellationToken`可以用來觀察非同步工作的取消作業。 `OnNavigateAsync`當使用者流覽至不同的頁面時，自動取消目前正在執行的導覽工作。

在中 `OnNavigateAsync` ，執行邏輯以決定要載入的元件。 選項包括：

* 方法內的條件式檢查 `OnNavigateAsync` 。
* 對應至元件名稱之路由的查閱資料表，可以插入至元件或在區塊內執行 [`@code`](xref:mvc/views/razor#code) 。

`LazyAssemblyLoader`是一種架構提供的單一服務，可用於載入元件。 插入 `LazyAssemblyLoader` `Router` 元件：

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader`提供的 `LoadAssembliesAsync` 方法如下：

* 使用 JS interop 透過網路呼叫來提取元件。
* 將元件載入執行時間，在瀏覽器的 WebAssembly 上執行。

> [!NOTE]
> 架構的消極式載入實作為支援在伺服器上進行可呈現。 在自動呈現期間，會假設載入所有元件，包括標示為消極式載入的元件。

### <a name="user-interaction-with-navigating-content"></a>使用者與內容的互動 `<Navigating>`

載入元件時，這可能需要幾秒鐘的時間，因此 `Router` 元件可以向使用者指出頁面轉換已發生：

* 加入命名空間的指示詞 [`@using`](xref:mvc/views/razor#using) <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 。
* `<Navigating>`使用要在頁面轉換事件期間顯示的標記，將標記新增至元件。

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a>處理中的取消`OnNavigateAsync`

`NavigationContext`傳遞至回呼的物件 `OnNavigateAsync` 包含新的 `CancellationToken` 導覽事件發生時所設定的。 `OnNavigateAsync`當此解除標記設定為避免繼續在過期的導覽上執行回呼時，回呼必須擲回 `OnNavigateAsync` 。

如果使用者流覽至路由 A，然後立即路由 B，則應用程式不應該繼續執行 `OnNavigateAsync` 路由 a 的回呼：

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> 如果取消中的取消 token `NavigationContext` 可能會導致非預期的行為，例如從上一個導覽呈現元件，則不會擲回。

### <a name="complete-example"></a>完整範例

下列完整 `Router` 元件示範 `GrantImaharaRobotControls.dll` 當使用者導覽至時，載入元件 `/robot` 。 在頁面轉換期間，使用者會看到樣式化的訊息。

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a>疑難排解

* 例如，如果發生未預期的轉譯 (例如，會) 呈現先前導覽中的元件，並確認如果已設定解除標記，程式碼就會擲回。
* 如果元件仍在應用程式啟動時載入，請檢查元件是否已在專案檔中標示為延遲載入。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/webassembly-performance-best-practices>
