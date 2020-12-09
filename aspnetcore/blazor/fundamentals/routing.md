---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 瞭解如何在應用程式和 NavLink 元件之間路由傳送要求。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 3bfd623a206f260d24e2c9009acdb3b205b7ab2d
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855400"
---
# <a name="aspnet-core-no-locblazor-routing"></a>ASP.NET Core Blazor 路由

作者：[Luke Latham](https://github.com/guardrex)

瞭解如何路由傳送要求，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件在應用程式中建立流覽連結 Blazor 。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 端點路由整合

Blazor Server 已整合至 [ASP.NET Core 端點路由](xref:fundamentals/routing)。 ASP.NET Core 應用程式設定為在中接受互動式元件的連入 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 連接 `Startup.Configure` ：

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最常見的設定是將所有要求路由傳送至 Razor 頁面，作為應用程式的伺服器端部分的主機 Blazor Server 。 依照慣例， *主機* 頁面通常會命名為 `_Host.cshtml` 。 主機檔案中指定的路由稱為「回溯 *路由* 」，因為它在路由比對中具有低優先順序。 當其他路由不相符時，就會考慮到回退路由。 這可讓應用程式使用其他控制器和頁面，而不會干擾 Blazor Server 應用程式。

如需針對 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 非根目錄 URL 伺服器裝載設定的詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。

## <a name="route-templates"></a>路由範本

<xref:Microsoft.AspNetCore.Components.Routing.Router>元件可讓您使用指定的路由路由傳送至每個元件。 <xref:Microsoft.AspNetCore.Components.Routing.Router>元件會出現在檔案中 `App.razor` ：

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

當編譯具有指示詞的檔案時 `.razor` `@page` ，系統會提供所產生的類別來 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 指定路由範本。 當應用程式啟動時，會掃描指定為的元件， `AppAssembly` 以收集具有的所有元件的相關資訊 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 。

在執行時間， <xref:Microsoft.AspNetCore.Components.RouteView> 元件：

* 從接收， <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> 以及任何所需的參數。
* 使用指定的參數，轉譯指定的元件及其版面配置 (或選擇性預設版面配置) 。

您可以選擇性地 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 使用版面配置類別指定參數，以用於未指定配置的元件。 預設 Blazor 範本會指定 `MainLayout` 元件。 `MainLayout.razor` 位於範本專案的資料夾中 `Shared` 。 如需版面配置的詳細資訊，請參閱 <xref:blazor/layouts> 。

您可以將多個路由範本套用至元件。 下列元件會回應和的要求 `/BlazorRoute` `/DifferentBlazorRoute` ：

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> 若要正確解析 Url，應用程式必須在其檔案中包含標籤 `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 以及 `href` 屬性 () 中指定的應用程式基底路徑 `<base href="/">` 。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/index#app-base-path>。

## <a name="provide-custom-content-when-content-isnt-found"></a>在找不到內容時提供自訂內容

<xref:Microsoft.AspNetCore.Components.Routing.Router>如果找不到要求的路由內容，此元件可讓應用程式指定自訂內容。

在檔案中 `App.razor` ，于元件的範本參數中設定自訂內容 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> <xref:Microsoft.AspNetCore.Components.Routing.Router> ：

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

標記的內容 `<NotFound>` 可以包含任意專案，例如其他互動式元件。 若要將預設版面配置套用至 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容，請參閱 <xref:blazor/layouts> 。

## <a name="route-to-components-from-multiple-assemblies"></a>從多個元件路由至元件

使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 參數來指定在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 搜尋可路由元件時，要考慮的元件的其他元件。 除了指定的元件之外，還會考慮指定的元件 `AppAssembly` 。 在下列範例中， `Component1` 是在參考的類別庫中定義的可路由元件。 下列 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 範例會產生的路由支援 `Component1` ：

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>路由參數

路由器會使用路由參數，以相同的名稱填入對應的元件參數， (不區分大小寫) 。

::: moniker range=">= aspnetcore-5.0"

支援選擇性參數。 在下列範例中， `text` 選擇性參數會將路由區段的值指派給元件的 `Text` 屬性。 如果區段不存在，的值 `Text` 會設定為 `fantastic` ：

```razor
@page "/RouteParameter/{text?}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

不支援選擇性參數。 上述範例會套用兩個指示詞 `@page` 。 第一個可讓您在不使用參數的情況下流覽至元件。 第二個指示詞 `@page` 會接受 `{text}` route 參數，並將值指派給 `Text` 屬性。

::: moniker-end

使用 on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) （而不是） [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 允許使用不同的選擇性參數值，將應用程式流覽至相同的元件。 根據上述範例， `OnParametersSet` 當使用者應該要能夠從移至或移至時，請使用 `/RouteParameter` `/RouteParameter/awesome` `/RouteParameter/awesome` `/RouteParameter` ：

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>路由條件約束

路由條件約束會強制將路由區段上的類型比對至元件。

在下列範例中，元件的路由 `Users` 只會符合下列條件：

* `Id`要求 URL 上有路由區段。
* `Id`區段是 () 的整數 `int` 。

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

下表中顯示的路由條件約束為可用。 針對符合非變異文化特性的路由條件約束，請參閱下表中的警告以取得詳細資訊。

| 條件約束 | 範例           | 範例相符項目                                                                  | 非變異值<br>culture<br>比對 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | 否                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | 是                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | 是                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | 是                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | 是                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 否                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | 是                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | 是                              |

> [!WARNING]
> 確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 <xref:System.DateTime>) 一律使用不因國別而異的文化特性。 這些條件約束假設 URL 不可當地語系化。

### <a name="routing-with-urls-that-contain-dots"></a>使用包含點的 Url 路由傳送

若是託管 Blazor WebAssembly 和 Blazor Server 應用程式，伺服器端預設路由範本會假設要求 URL 的最後一個區段包含點 (`.`)  (例如 `https://localhost.com:5001/example/some.thing`) 。 如果沒有額外的設定，應用程式如果要路由傳送至元件，則會傳回 *404-找不* 到的回應。 若要使用具有一個或多個包含點之參數的路由，應用程式必須使用自訂範本來設定路由。

請考慮下列 `Example` 可從 URL 最後一個區段接收路由參數的元件：

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

若要允許裝載解決方案的 *伺服器* 應用程式 Blazor WebAssembly 使用參數中的點來路由傳送要求 `param` ，請在 () 中新增具有選擇性參數的回溯檔案路由範本 `Startup.Configure` `Startup.cs` ：

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

若要設定 Blazor Server 應用程式以在參數中將要求路由傳送至某個點 `param` ，請在 () 中新增具有選擇性參數的 fallback 頁面路由範本 `Startup.Configure` `Startup.cs` ：

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

如需詳細資訊，請參閱<xref:fundamentals/routing>。

## <a name="catch-all-route-parameters"></a>Catch-all 路由參數

::: moniker range=">= aspnetcore-5.0"

*本節適用于 .NET 5 候選版 1 (RC1) 或更新版本中的 ASP.NET Core。*

元件中支援攔截所有路由參數，這些參數會跨多個資料夾界限來捕捉路徑。 全部攔截路由參數必須是：

* 命名為與路由區段名稱相符。 命名不區分大小寫。
* `string` 型別。 架構不會提供自動轉換。
* 在 URL 的結尾。

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

`/page/this/is/a/test`若為具有路由範本的 URL `/page/{*pageRoute}` ，的值 `PageRoute` 會設定為 `this/is/a/test` 。

已解碼之已捕捉路徑的斜線和區段。 若為的路由範本 `/page/{*pageRoute}` ，URL 會 `/page/this/is/a%2Ftest%2A` 產生 `this/is/a/test*` 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

ASP.NET Core 5.0 或更新版本中支援 Catch-all 路由參數。

::: moniker-end

## <a name="navlink-component"></a>NavLink 元件

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>建立導覽連結時，請使用元件來取代 HTML 超連結元素 (`<a>`) 。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>元件的行為類似專案 `<a>` ，但它會 `active` 根據其是否 `href` 符合目前的 URL 來切換 CSS 類別。 `active`類別可協助使用者瞭解在顯示的導覽連結中，哪個頁面是使用中的頁面。 （選擇性）將 CSS 類別名稱指派為， <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> 以便在目前的路由符合時將自訂 css 類別套用至轉譯的連結 `href` 。

下列 `NavMenu` 元件 [`Bootstrap`](https://getbootstrap.com/docs/) 會建立示範如何使用元件的巡覽列 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ：

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

有兩個 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 選項可供您指派給 `Match` 元素的屬性 `<NavLink>` ：

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當符合整個目前的 URL 時，會處於作用中狀態。
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*預設*) ： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當它符合目前 URL 的任何前置詞時，就會處於作用中狀態。

在上述範例中，Home 會 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 符合 home URL，而且只會 `active` 在應用程式的預設基底路徑 URL 上接收 CSS 類別 (例如 `https://localhost:5001/`) 。 第二個會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` 使用者造訪具有前置詞 (的任何 URL 時收到類別 `MyComponent` ，例如， `https://localhost:5001/MyComponent` 以及 `https://localhost:5001/MyComponent/AnotherSegment`) 。

其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件屬性會傳遞至轉譯的錨點標記。 在下列範例中， <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件包含 `target` 屬性：

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

下列 HTML 標籤會呈現：

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> 由於轉譯 Blazor 子內容的方式， `NavLink` 如果在 `for` `NavLink` (子) 元件的內容中使用遞增迴圈變數，則迴圈內的轉譯元件需要本機索引變數：
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> 在此案例中使用索引變數，是在其子 [內容](xref:blazor/components/index#child-content)中使用迴圈變數的 **任何** 子元件（而不只是元件）的需求 `NavLink` 。
>
> 或者，使用 `foreach` 迴圈搭配 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> ：
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>URI 和流覽狀態協助程式

用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 來處理 c # 程式碼中的 uri 和導覽。 <xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。

| member | 描述 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | 取得目前的絕對 URI。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | 取得基底 URI (，其尾端斜線) 可在相對 URI 路徑前面加上，以產生絕對 URI。 一般而言，會 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 對應至 `href` `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 中檔元素上的屬性 Blazor Server 。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | 流覽至指定的 URI。 如果 `forceLoad` 為 `true` ：<ul><li>略過用戶端路由。</li><li>因為 URI 通常是由用戶端路由器處理，所以會強制瀏覽器從伺服器載入新的頁面。</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | 當導覽位置變更時引發的事件。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | 將相對 URI 轉換為絕對 URI。 |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | 假設基底 URI (例如，) 先前傳回的 URI <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ，會將絕對 uri 轉換成相對於基底 uri 前置詞的 uri。 |

當選取按鈕時，下列元件會流覽至應用程式的 `Counter` 元件：

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

下列元件會透過訂閱來處理位置已變更的事件 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 。 `HandleLocationChanged`當架構呼叫時，方法會解除掛鉤 `Dispose` 。 Unhooking 方法允許元件的垃圾收集。

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 提供有關事件的下列資訊：

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果 `true` ， Blazor 從瀏覽器攔截導覽。 如果 `false` 為，則 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 會造成導覽。

如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。

## <a name="query-string-and-parse-parameters"></a>查詢字串和剖析參數

您可以從的屬性取得要求的查詢字串 <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> ：

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

剖析查詢字串的參數：

* 新增 [AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)的套件參考。
* 使用剖析查詢字串之後，取得值 <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> 。

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

`{KEY}`上述範例中的預留位置是查詢字串參數索引鍵。 例如，URL 索引鍵/值組會 `?ship=Tardis` 使用的索引鍵 `ship` 。
