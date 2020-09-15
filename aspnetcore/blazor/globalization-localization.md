---
title: ASP.NET Core Blazor 全球化和當地語系化
author: guardrex
description: 瞭解如何讓 Razor 多個文化特性和語言的使用者都能存取元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 2b8820acba564bdfb85f8338ed5482573960fbb4
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080273"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor 全球化和當地語系化

依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Razor 有多種文化特性和語言的使用者可以存取元件。 以下是可用的 .NET 全球化和當地語系化案例：

* .NET 的資源系統
* 特定文化特性的數位和日期格式

目前支援一組有限的 ASP.NET Core 當地語系化案例：

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 在應用程式中受到 *支援* Blazor 。
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>、 <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 和資料批註當地語系化是 ASP.NET CORE MVC 案例，而且 **不支援** Blazor 應用程式。

如需詳細資訊，請參閱<xref:fundamentals/localization>。

## <a name="globalization"></a>全球化

Blazor的 [`@bind`](xref:mvc/views/razor#bind) 功能會根據使用者目前的文化特性來執行格式和剖析顯示的值。

您可以從屬性存取目前的文化 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 特性。

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> 用於下欄欄位類型 (`<input type="{TYPE}" />`) ：

* `date`
* `number`

上述欄位類型：

* 會使用其適當的瀏覽器型格式規則來顯示。
* 不能包含自由格式的文字。
* 根據瀏覽器的執行提供使用者互動特性。

下欄欄位類型具有特定的格式設定需求，而且目前不支援， Blazor 因為所有主要瀏覽器都不支援這些類型：

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) 支援 `@bind:culture` 參數，以提供 <xref:System.Globalization.CultureInfo?displayProperty=fullName> 剖析和格式化值的。 使用 `date` 和欄位類型時，不建議使用指定文化特性 `number` 。 `date` 而且 `number` 具有內建的 Blazor 支援，可提供必要的文化特性。

## <a name="localization"></a>當地語系化

### Blazor WebAssembly

Blazor WebAssembly 應用程式會使用使用者的 [語言喜好](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)設定來設定文化特性。

若要明確設定文化特性， <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> 請 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> 在中設定和 `Program.Main` 。

::: moniker range=">= aspnetcore-5.0"

依預設，會 Blazor WebAssembly 在使用者的文化特性中攜帶顯示值所需的全球化資源，例如日期和貨幣。 如果應用程式不需要當地語系化，您可以設定應用程式以支援以文化特性為基礎的非變異文化特性 `en-US` ：

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

根據預設，應用程式的中繼語言 (IL) 連結器 Blazor WebAssembly 設定會去除國際化資訊（明確要求的地區設定除外）。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>。

::: moniker-end

雖然預設選取的文化特性 Blazor 對大部分的使用者而言就已足夠，但請考慮提供方法讓使用者指定其慣用的地區設定。 如需 Blazor WebAssembly 使用文化特性選擇器的範例應用程式，請參閱 [`LocSample`](https://github.com/pranavkm/LocSample) 當地語系化範例應用程式。

### Blazor Server

Blazor Server 應用程式會使用 [當地語系化中介軟體](xref:fundamentals/localization#localization-middleware)進行當地語系化。 中介軟體會為從應用程式要求資源的使用者選取適當的文化特性。

您可以使用下列其中一種方法來設定文化特性：

* [Cookie！](#cookies)
* [提供 UI 以選擇文化特性](#provide-ui-to-choose-the-culture)

如需詳細資訊和範例，請參閱 <xref:fundamentals/localization>。

#### <a name="no-loccookies"></a>Cookie

當地語系化文化特性 cookie 可保存使用者的文化特性。 當地語系化中介軟體會 cookie 在後續要求中讀取，以設定使用者的文化特性。 

使用 cookie 可確保 WebSocket 連接可以正確傳播文化特性。 如果當地語系化架構是以 URL 路徑或查詢字串為基礎，則配置可能無法使用 Websocket，因此無法保存文化特性。 因此，建議的方法是使用當地語系化文化特性 cookie 。

如果文化特性是以當地語系化方式保存，任何技術都可以用來指派文化特性 cookie 。 如果應用程式已經為伺服器端 ASP.NET Core 建立了當地語系化配置，請繼續使用應用程式的現有當地語系化基礎結構，並在 cookie 應用程式的配置內設定當地語系化文化特性。

下列範例示範如何在中設定 cookie 可由當地語系化中介軟體讀取的目前文化特性。 在檔案的 Razor `Pages/_Host.cshtml` 開頭標記內立即建立運算式 `<body>` ：

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

應用程式會以下列事件順序來處理當地語系化：

1. 瀏覽器會將初始 HTTP 要求傳送至應用程式。
1. 文化特性是由當地語系化中介軟體所指派。
1. Razor頁面中的運算式 `_Host` (`_Host.cshtml`) 會在中將文化特性保存 cookie 為回應的一部分。
1. 瀏覽器會開啟 WebSocket 連接來建立互動式 Blazor Server 會話。
1. 當地語系化中介軟體會讀取 cookie 並指派文化特性。
1. Blazor Server會話的開頭是正確的文化特性。

#### <a name="provide-ui-to-choose-the-culture"></a>提供 UI 以選擇文化特性

若要提供 UI 讓使用者選取文化特性，建議使用以重新 *導向為基礎的方法* 。 此程式類似于當使用者嘗試存取安全資源時，web 應用程式中所發生的情況。 使用者會重新導向至登入頁面，然後重新導向回到原始資源。 

應用程式會透過重新導向至控制器來保存使用者選取的文化特性。 控制器會將使用者的選取文化特性設定為 cookie ，並將使用者重新導向回原始的 URI。

在伺服器上建立 HTTP 端點，以設定使用者在中所選取的文化特性 cookie ，然後重新導向回原始的 URI：

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> 使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 動作結果來防止開啟重新導向攻擊。 如需詳細資訊，請參閱<xref:security/preventing-open-redirects>。

如果應用程式未設定為處理控制器動作：

* 將 MVC 服務新增至中的服務集合 `Startup.ConfigureServices` ：

  ```csharp
  services.AddControllers();
  ```

* 在下列情況中新增控制器端點路由 `Startup.Configure` ：

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

下列元件顯示如何在使用者選取文化特性時執行初始重新導向的範例：

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/localization>
