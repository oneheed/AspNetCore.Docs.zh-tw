---
title: ASP.NET Core 中的延遲載入元件 Blazor WebAssembly
author: guardrex
description: 探索如何在 ASP.NET Core 應用程式中延遲載入元件 Blazor WebAssembly 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 46f98080ad40f614f9cb1af2190f263d205c1016
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865164"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core 中的延遲載入元件 Blazor WebAssembly

依 [Safia Abdalla](https://safia.rocks) 和 [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly 應用程式啟動效能可透過延後載入部分應用程式元件來改善，直到需要它們為止，這稱為「消極式 *載入*」。 例如，只有在使用者流覽至該元件時，才可以設定僅用來呈現單一元件的元件。 載入之後，元件會快取用戶端，並可供所有未來的導覽使用。

Blazor的消極式載入功能可讓您將應用程式元件標記為消極式載入，這會在使用者流覽至特定路由時，于執行時間載入元件。 此功能包含對專案檔的變更，以及對應用程式路由器的變更。

> [!NOTE]
> 元件消極式載入不會讓應用程式受益， Blazor Server 因為元件不會下載到應用程式中的用戶端 Blazor Server 。

## <a name="project-file"></a>專案檔

使用專案將應用程式專案檔中的消極式載入的元件標記 (`.csproj`) `BlazorWebAssemblyLazyLoad` 。 使用沒有副檔名的元件名稱 `.dll` 。 Blazor架構會防止此專案群組所指定的元件在應用程式啟動時載入。 下列範例會將大型自訂群組件標示 `GrantImaharaRobotControls.dll` 為延遲載入 () 。 如果標示為消極式載入的元件有相依性，則也必須在專案檔中將它們標示為消極式載入。

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

只有應用程式使用的元件可以延遲載入。 連結器會從已發佈的輸出中去除未使用的元件。

> [!NOTE]
> 在 .NET 5 候選版 1 (RC1) 或更新版本（將于九月推出），元件名稱將需要 `.dll` 副檔名：
>
> ```xml
> <ItemGroup>
>  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
> </ItemGroup>
> ```

## <a name="router-component"></a>`Router` 元件

Blazor的 `Router` 元件會指定哪些元件會 Blazor 搜尋可路由傳送的元件。 `Router`元件也負責轉譯使用者導覽之路由的元件。 `Router`元件支援 `OnNavigateAsync` 可與消極式載入搭配使用的功能。

在應用程式的 `Router` 元件中 (`App.razor`) ：

* 新增 `OnNavigateAsync` 回呼。 `OnNavigateAsync`當使用者執行下列動作時，就會叫用處理程式：
  * 第一次造訪路由，方法是直接從瀏覽器流覽至。
  * 使用連結或調用來流覽至新的路由 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。
* 如果延遲載入的元件包含可路由傳送的元件，請將[清單](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (例如，將) 命名為 `lazyLoadedAssemblies` 元件。 如果元件包含可路由傳送的元件，這些元件就會傳回 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 集合。 架構會在元件中搜尋路由，並在找到任何新的路由時更新路由集合。

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

### <a name="assembly-load-logic-in-onnavigateasync"></a>中的元件載入邏輯 `OnNavigateAsync`

`OnNavigateAsync` 具有 `NavigationContext` 提供目前非同步導覽事件相關資訊的參數，包括目標路徑 (`Path`) ，以及 () 的解除標記 `CancellationToken` ：

* `Path`屬性是相對於應用程式基底路徑的使用者目的地路徑，例如 `/robot` 。
* `CancellationToken`可以用來觀察非同步工作的取消。 `OnNavigateAsync` 當使用者流覽至另一個頁面時，會自動取消目前正在執行的流覽工作。

在內部 `OnNavigateAsync` ，會執行邏輯來判斷要載入的元件。 這些選項包括：

* 方法內的條件式檢查 `OnNavigateAsync` 。
* 對應至元件名稱之路由的查閱資料表，可插入元件或在區塊內執行 [`@code`](xref:mvc/views/razor#code) 。

`LazyAssemblyLoader` 是用於載入元件的架構提供的單一服務。 插入 `LazyAssemblyLoader` `Router` 元件：

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader`提供 `LoadAssembliesAsync` 方法：

* 使用 JS interop 透過網路呼叫來提取元件。
* 將元件載入至在瀏覽器中于 WebAssembly 上執行的執行時間。

> [!NOTE]
> 架構的消極式載入執行支援在伺服器上進行可呈現。 在預進行期間，會假設載入所有元件，包括標示為消極式載入的元件。

### <a name="user-interaction-with-navigating-content"></a>使用者與內容的互動 `<Navigating>`

載入元件（可能需要幾秒鐘的時間）時， `Router` 元件可能會向使用者表示頁面轉換發生：

* 新增命名空間的指示詞 [`@using`](xref:mvc/views/razor#using) <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 。
* 將 `<Navigating>` 標記新增至元件，並在頁面轉換事件期間顯示標記。

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

### <a name="handle-cancellations-in-onnavigateasync"></a>處理取消 `OnNavigateAsync`

`NavigationContext`傳遞至回呼的物件 `OnNavigateAsync` 包含新的 `CancellationToken` 流覽事件發生時所設定的。 `OnNavigateAsync`回呼必須在設定此解除標記時擲回，以避免 `OnNavigateAsync` 在過期的導覽上繼續執行回呼。

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
> 如果取消中的解除標記 `NavigationContext` 可能會導致非預期的行為，例如從先前的導覽呈現元件，則不會擲回。

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync` 事件和重新命名的元件檔案

資源載入器會依賴檔案中定義的元件名稱 `blazor.boot.json` 。 如果重新 [命名元件](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)，則方法中使用的元件名稱 `OnNavigateAsync` 和檔案中的元件名稱 `blazor.boot.json` 將不會同步。

若要修正此情況：

* 檢查應用程式是否正在生產環境中執行，以判斷要使用的元件名稱。
* 將重新命名的元件名稱儲存在另一個檔案中，並從該檔案讀取，以判斷要在和方法中使用的元件名稱 `LazyLoadAssemblyService` `OnNavigateAsync` 。

### <a name="complete-example"></a>完整範例

下列完整 `Router` 元件示範在 `GrantImaharaRobotControls.dll` 使用者流覽至時載入元件 `/robot` 。 在頁面轉換期間，會向使用者顯示樣式的訊息。

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

* 如果發生非預期的轉譯 (例如，先前導覽中的元件會轉譯) ，確認程式碼會在已設定解除標記時擲回。
* 如果元件仍在應用程式啟動時載入，請確認元件在專案檔中已標示為延遲載入。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/webassembly-performance-best-practices>
