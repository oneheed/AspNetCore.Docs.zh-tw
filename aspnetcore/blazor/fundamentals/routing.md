---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 瞭解如何管理應用程式中的要求路由，以及如何在應用程式中使用 NavLink 元件 Blazor 進行導覽。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
ms.openlocfilehash: 960da99b6ed2ab0d71d13c443239b0f19ac88a49
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554859"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 路由

在本文中，您將瞭解如何管理要求路由，以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件在應用程式中建立流覽連結 Blazor 。

## <a name="route-templates"></a>路由範本

<xref:Microsoft.AspNetCore.Components.Routing.Router>元件可讓您路由傳送至 Razor 應用程式中的元件 Blazor 。 <xref:Microsoft.AspNetCore.Components.Routing.Router>元件會用於 `App` Blazor 應用程式的元件。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

當編譯具有指示詞的 Razor 元件 (`.razor`) [ `@page` 時](xref:mvc/views/razor#page)，會提供所產生的元件類別來 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 指定元件的路由範本。

當應用程式啟動時，會掃描指定為路由器的元件， `AppAssembly` 以收集具有的應用程式元件的路由資訊 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 。

在執行時間， <xref:Microsoft.AspNetCore.Components.RouteView> 元件：

* 從接收， <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> 連同任何路由參數。
* 以其配置呈現指定的元件，包括任何 [進一步的嵌套](xref:blazor/layouts)版面配置。

選擇性地 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 針對未使用指示詞指定配置的元件，使用配置類別來指定[ `@layout` ](xref:blazor/layouts#apply-a-layout-to-a-component)參數。 架構的[ Blazor 專案範本](xref:blazor/project-structure)會將 `MainLayout` 元件 (`Shared/MainLayout.razor`) 指定為應用程式的預設版面配置。 如需版面配置的詳細資訊，請參閱 <xref:blazor/layouts> 。

元件支援使用多個指示詞[ `@page` 的多個路由](xref:mvc/views/razor#page)範本。 下列範例元件會載入和的要求 `/BlazorRoute` `/DifferentBlazorRoute` 。

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> 若要正確解析 Url，應用程式必須在其檔案中包含標籤 `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 以及屬性中指定的應用程式基底路徑 `href` 。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/index#app-base-path>。

## <a name="provide-custom-content-when-content-isnt-found"></a>在找不到內容時提供自訂內容

<xref:Microsoft.AspNetCore.Components.Routing.Router>如果找不到要求的路由內容，此元件可讓應用程式指定自訂內容。

在元件的元件 `App` 範本中，設定自訂內容 <xref:Microsoft.AspNetCore.Components.Routing.Router> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

以標記的內容支援任意專案 `<NotFound>` ，例如其他互動式元件。 若要將預設版面配置套用至 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容，請參閱 <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component> 。

## <a name="route-to-components-from-multiple-assemblies"></a>從多個元件路由至元件

使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 參數來指定在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 搜尋可路由元件時，要考慮的元件的其他元件。 除了指定的元件之外，還會掃描額外的元件 `AppAssembly` 。 在下列範例中， `Component1` 是在參考 [元件類別庫](xref:blazor/components/class-libraries)中定義的可路由元件。 下列 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 範例會產生的路由支援 `Component1` 。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a>路由參數

路由器會使用路由參數，以相同的名稱填入對應的 [元件參數](xref:blazor/components/index#component-parameters) 。 路由參數名稱不區分大小寫。 在下列範例中，參數會將 `text` 路由區段的值指派給元件的 `Text` 屬性。 當提出要求時 `/RouteParameter/amazing` ， `<h1>` 標記內容會轉譯為 `Blazor is amazing!` 。

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

支援選擇性參數。 在下列範例中， `text` 選擇性參數會將路由區段的值指派給元件的 `Text` 屬性。 如果區段不存在，的值 `Text` 會設定為 `fantastic` 。

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

不支援選擇性參數。 下列範例會套用兩個[ `@page` ](xref:mvc/views/razor#page)指示詞。 第一個指示詞允許在不使用參數的情況下流覽至元件。 第二個指示詞會將 `{text}` 路由參數值指派給元件的 `Text` 屬性。

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

使用 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set-onparameterssetasync) 取代 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods-oninitializedasync) 來允許使用不同的選擇性參數值，將應用程式流覽至相同的元件。 根據上述範例， `OnParametersSet` 當使用者應該要能夠從移至或移至時，請使用 `/RouteParameter` `/RouteParameter/amazing` `/RouteParameter/amazing` `/RouteParameter` ：

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>路由條件約束

路由條件約束會強制將路由區段上的類型比對至元件。

在下列範例中，元件的路由 `User` 只會符合下列條件：

* `Id`要求 URL 中有路由區段。
* `Id`區段是 (`int`) 類型的整數。

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

下表中顯示的路由條件約束為可用。 針對符合不因文化特性而異的路由條件約束，請參閱下表中的警告以取得詳細資訊。

| 條件約束 | 範例           | 範例相符項目                                                                  | 非變異值<br>culture<br>比對 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |

> [!WARNING]
> 確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 <xref:System.DateTime>) 一律使用不因國別而異的文化特性。 這些條件約束假設 URL 不可當地語系化。

## <a name="routing-with-urls-that-contain-dots"></a>使用包含點的 Url 路由傳送

針對託管 Blazor WebAssembly 和 Blazor Server 應用程式，伺服器端的預設路由範本會假設要求 URL 的最後一個區段包含點 (`.`) 要求的檔案。 例如，路由器會將 URL 解釋為名為之檔案的 `https://localhost.com:5001/example/some.thing` 要求 `some.thing` 。 如果您想要使用指示詞 `some.thing` 路由傳送至元件， [ `@page` 而不](xref:mvc/views/razor#page) `some.thing` 是路由參數值，則應用程式不需要額外設定，就會傳回 404-找不到的回應。 若要使用具有一個或多個包含點之參數的路由，應用程式必須使用自訂範本來設定路由。

請考慮下列 `Example` 可從 URL 最後一個區段接收路由參數的元件。

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

若要允許 **`Server`** 託管解決方案的應用程式 Blazor WebAssembly 使用 route 參數中的點來路由傳送要求 `param` ，請在中新增具有選擇性參數的回溯檔案路由範本 `Startup.Configure` 。

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

若要將 Blazor Server 應用程式設定為使用路由參數中的點來路由傳送要求 `param` ，請在中新增具有選擇性參數的回溯頁面路由範本 `Startup.Configure` 。

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

如需詳細資訊，請參閱<xref:fundamentals/routing>。

## <a name="catch-all-route-parameters"></a>Catch-all 路由參數

::: moniker range=">= aspnetcore-5.0"

元件中支援攔截所有路由參數，這些參數會跨多個資料夾界限來捕捉路徑。

Catch-all 路由參數為：

* 命名為與路由區段名稱相符。 命名不區分大小寫。
* `string` 型別。 架構不會提供自動轉換。
* 在 URL 的結尾。

`Pages/CatchAll.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

`/catch-all/this/is/a/test`若為具有路由範本的 URL `/catch-all/{*pageRoute}` ，的值 `PageRoute` 會設定為 `this/is/a/test` 。

已解碼之已捕捉路徑的斜線和區段。 若為的路由範本 `/catch-all/{*pageRoute}` ，URL 會 `/catch-all/this/is/a%2Ftest%2A` 產生 `this/is/a/test*` 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

ASP.NET Core 5.0 或更新版本中支援 Catch-all 路由參數。 如需詳細資訊，請選取此文章的5.0 版本。

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>URI 和流覽狀態協助程式

用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 來管理 c # 程式碼中的 uri 和導覽。 <xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。

| member | 描述 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | 取得目前的絕對 URI。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | 取得基底 URI (，其尾端斜線) 可在相對 URI 路徑前面加上，以產生絕對 URI。 一般而言，會 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 對應至 `href` `<base>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 中檔元素上的屬性 Blazor Server 。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | 流覽至指定的 URI。 如果 `forceLoad` 為 `true` ：<ul><li>略過用戶端路由。</li><li>因為 URI 通常是由用戶端路由器處理，所以會強制瀏覽器從伺服器載入新的頁面。</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | 當導覽位置變更時引發的事件。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | 將相對 URI 轉換為絕對 URI。 |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | 假設基底 URI (例如，) 先前傳回的 URI <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ，會將絕對 uri 轉換成相對於基底 uri 前置詞的 uri。 |

針對 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 事件， <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 提供下列有關導覽事件的資訊：

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果 `true` ， Blazor 從瀏覽器攔截導覽。 如果 `false` 為，則 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 會造成導覽。

下列元件：

* 使用選取按鈕時，流覽至應用程式的 `Counter` 元件 (`Pages/Counter.razor`) <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> 。
* 藉由訂閱來處理位置已變更的事件 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 。
  * `HandleLocationChanged`當架構呼叫時，方法會解除掛鉤 `Dispose` 。 Unhooking 方法允許元件的垃圾收集。
  * 當選取按鈕時，記錄器執行會記錄下列資訊：

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。

## <a name="query-string-and-parse-parameters"></a>查詢字串和剖析參數

您可以從屬性取得要求的查詢字串 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> ：

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

若要剖析查詢字串的參數，其中一個方法是搭配 [`URLSearchParams`](https://developer.mozilla.org/docs/Web/API/URLSearchParams) 使用 [JAVASCRIPT (JS) interop](xref:blazor/call-javascript-from-dotnet)：

```javascript
export createQueryString = (string queryString) => new URLSearchParams(queryString);
```

如需詳細資訊，請參閱[ Blazor JavaScript 隔離和物件參考](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。

## <a name="navlink-and-navmenu-components"></a>`NavLink` 和 `NavMenu` 元件

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>建立導覽連結時，請使用元件來取代 HTML 超連結元素 (`<a>`) 。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>元件的行為類似專案 `<a>` ，但它會 `active` 根據其是否 `href` 符合目前的 URL 來切換 CSS 類別。 `active`類別可協助使用者瞭解在顯示的導覽連結中，哪個頁面是使用中的頁面。 （選擇性）將 CSS 類別名稱指派為， <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> 以便在目前的路由符合時將自訂 css 類別套用至轉譯的連結 `href` 。

下列 `NavMenu` 元件 [`Bootstrap`](https://getbootstrap.com/docs/) 會建立示範如何使用元件的巡覽列 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> `NavMenu`元件 (`NavMenu.razor`) 會提供在 `Shared` [ Blazor 專案範本](xref:blazor/project-structure)所產生之應用程式的資料夾中。

有兩個 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 選項可供您指派給 `Match` 元素的屬性 `<NavLink>` ：

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當符合整個目前的 URL 時，會處於作用中狀態。
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*預設*) ： <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 當它符合目前 URL 的任何前置詞時，就會處於作用中狀態。

在上述範例中，Home 會 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 符合 home URL，而且只會 `active` 在應用程式的預設基底路徑 URL 上接收 CSS 類別 (例如 `https://localhost:5001/`) 。 第二個會在 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `active` 使用者造訪具有前置詞 (的任何 URL 時收到類別 `component` ，例如， `https://localhost:5001/component` 以及 `https://localhost:5001/component/another-segment`) 。

其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件屬性會傳遞至轉譯的錨點標記。 在下列範例中， <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 元件包含 `target` 屬性：

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

下列 HTML 標籤會呈現：

```html
<a href="example-page" target="_blank">Example page</a>
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

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 端點路由整合

*本節僅適用于 Blazor Server 應用程式。*

Blazor Server 已整合至 [ASP.NET Core 端點路由](xref:fundamentals/routing)。 ASP.NET Core 應用程式設定為在中接受互動式元件的連入 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 連線 `Startup.Configure` 。

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

一般設定是將所有要求路由傳送至 Razor 頁面，作為應用程式的伺服器端部分的主機 Blazor Server 。 依照慣例， *主機* 頁面通常會 `_Host.cshtml` 在 `Pages` 應用程式的資料夾中命名。

主機檔案中指定的路由稱為「回溯 *路由* 」，因為它在路由比對中具有低優先順序。 當其他路由不相符時，就會使用 fallback 路由。 這可讓應用程式使用其他控制器和頁面，而不會干擾應用程式中的元件路由 Blazor Server 。

如需針對 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 非根目錄 URL 伺服器裝載設定的詳細資訊，請參閱 <xref:blazor/host-and-deploy/index#app-base-path> 。
