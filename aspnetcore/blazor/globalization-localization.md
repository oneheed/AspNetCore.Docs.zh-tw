---
title: 'ASP.NET Core :::no-loc(Blazor)::: 全球化和當地語系化'
author: guardrex
description: '瞭解如何讓 :::no-loc(Razor)::: 多個文化特性和語言的使用者都能存取元件。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/globalization-localization
ms.openlocfilehash: f8f261f25d854a9bf36ad3299f4af392d5c4fafd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055876"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="56aa1-103">ASP.NET Core :::no-loc(Blazor)::: 全球化和當地語系化</span><span class="sxs-lookup"><span data-stu-id="56aa1-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="56aa1-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="56aa1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="56aa1-105">:::no-loc(Razor)::: 有多種文化特性和語言的使用者可以存取元件。</span><span class="sxs-lookup"><span data-stu-id="56aa1-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="56aa1-106">以下是可用的 .NET 全球化和當地語系化案例：</span><span class="sxs-lookup"><span data-stu-id="56aa1-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="56aa1-107">.NET 的資源系統</span><span class="sxs-lookup"><span data-stu-id="56aa1-107">.NET's resources system</span></span>
* <span data-ttu-id="56aa1-108">特定文化特性的數位和日期格式</span><span class="sxs-lookup"><span data-stu-id="56aa1-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="56aa1-109">目前支援一組有限的 ASP.NET Core 當地語系化案例：</span><span class="sxs-lookup"><span data-stu-id="56aa1-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="56aa1-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 在應用程式中受到 *支援* :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="56aa1-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>、 <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 和資料批註當地語系化是 ASP.NET CORE MVC 案例，而且 **不支援** :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="56aa1-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="56aa1-112">如需詳細資訊，請參閱<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="56aa1-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="56aa1-113">全球化</span><span class="sxs-lookup"><span data-stu-id="56aa1-113">Globalization</span></span>

<span data-ttu-id="56aa1-114">:::no-loc(Blazor):::的 [`@bind`](xref:mvc/views/razor#bind) 功能會根據使用者目前的文化特性來執行格式和剖析顯示的值。</span><span class="sxs-lookup"><span data-stu-id="56aa1-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="56aa1-115">您可以從屬性存取目前的文化 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="56aa1-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> 用於下欄欄位類型 (`<input type="{TYPE}" />`) ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="56aa1-117">上述欄位類型：</span><span class="sxs-lookup"><span data-stu-id="56aa1-117">The preceding field types:</span></span>

* <span data-ttu-id="56aa1-118">會使用其適當的瀏覽器型格式規則來顯示。</span><span class="sxs-lookup"><span data-stu-id="56aa1-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="56aa1-119">不能包含自由格式的文字。</span><span class="sxs-lookup"><span data-stu-id="56aa1-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="56aa1-120">根據瀏覽器的執行提供使用者互動特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="56aa1-121">下欄欄位類型具有特定的格式設定需求，而且目前不支援， :::no-loc(Blazor)::: 因為所有主要瀏覽器都不支援這些類型：</span><span class="sxs-lookup"><span data-stu-id="56aa1-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="56aa1-122">[`@bind`](xref:mvc/views/razor#bind) 支援 `@bind:culture` 參數，以提供 <xref:System.Globalization.CultureInfo?displayProperty=fullName> 剖析和格式化值的。</span><span class="sxs-lookup"><span data-stu-id="56aa1-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="56aa1-123">使用 `date` 和欄位類型時，不建議使用指定文化特性 `number` 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="56aa1-124">`date` 而且 `number` 具有內建的 :::no-loc(Blazor)::: 支援，可提供必要的文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="56aa1-125">Localization</span><span class="sxs-lookup"><span data-stu-id="56aa1-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="56aa1-126">:::no-loc(Blazor WebAssembly)::: 應用程式會使用使用者的 [語言喜好](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)設定來設定文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="56aa1-127">若要明確設定文化特性， <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> 請 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> 在中設定和 `Program.Main` 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="56aa1-128">根據預設，會 :::no-loc(Blazor WebAssembly)::: 攜帶在使用者的文化特性中顯示值所需的最基本全球化資源，例如日期和貨幣。</span><span class="sxs-lookup"><span data-stu-id="56aa1-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="56aa1-129">必須在專案檔中設定支援動態變更文化特性的應用程式 `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="56aa1-130">:::no-loc(Blazor WebAssembly)::: 也可以使用傳遞至的選項，設定為使用特定應用程式文化特性啟動 `:::no-loc(Blazor):::.start` 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="56aa1-131">例如，以下範例顯示已設定為使用文化特性啟動的應用程式 `en-GB` ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="56aa1-132">的值 `applicationCulture` 應該符合 [BCP-47 語言標記格式](https://tools.ietf.org/html/bcp47)。</span><span class="sxs-lookup"><span data-stu-id="56aa1-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="56aa1-133">如果應用程式不需要當地語系化，您可以設定應用程式以支援以文化特性為基礎的非變異文化特性 `en-US` ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="56aa1-134">根據預設，應用程式的中繼語言 (IL) 連結器 :::no-loc(Blazor WebAssembly)::: 設定會去除國際化資訊（明確要求的地區設定除外）。</span><span class="sxs-lookup"><span data-stu-id="56aa1-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="56aa1-135">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>。</span><span class="sxs-lookup"><span data-stu-id="56aa1-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="56aa1-136">雖然預設選取的文化特性 :::no-loc(Blazor)::: 對大部分的使用者而言就已足夠，但請考慮提供方法讓使用者指定其慣用的地區設定。</span><span class="sxs-lookup"><span data-stu-id="56aa1-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="56aa1-137">如需 :::no-loc(Blazor WebAssembly)::: 使用文化特性選擇器的範例應用程式，請參閱 [`LocSample`](https://github.com/pranavkm/LocSample) 當地語系化範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="56aa1-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="56aa1-138">:::no-loc(Blazor Server)::: 應用程式會使用 [當地語系化中介軟體](xref:fundamentals/localization#localization-middleware)進行當地語系化。</span><span class="sxs-lookup"><span data-stu-id="56aa1-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="56aa1-139">中介軟體會為從應用程式要求資源的使用者選取適當的文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="56aa1-140">您可以使用下列其中一種方法來設定文化特性：</span><span class="sxs-lookup"><span data-stu-id="56aa1-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="56aa1-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="56aa1-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="56aa1-142">提供 UI 以選擇文化特性</span><span class="sxs-lookup"><span data-stu-id="56aa1-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="56aa1-143">如需詳細資訊和範例，請參閱 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="56aa1-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="56aa1-144">:::no-loc(Cookie):::</span><span class="sxs-lookup"><span data-stu-id="56aa1-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="56aa1-145">當地語系化文化特性 :::no-loc(cookie)::: 可保存使用者的文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="56aa1-146">當地語系化中介軟體會 :::no-loc(cookie)::: 在後續要求中讀取，以設定使用者的文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="56aa1-147">使用 :::no-loc(cookie)::: 可確保 WebSocket 連接可以正確傳播文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="56aa1-148">如果當地語系化架構是以 URL 路徑或查詢字串為基礎，則配置可能無法使用 Websocket，因此無法保存文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="56aa1-149">因此，建議的方法是使用當地語系化文化特性 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="56aa1-150">如果文化特性是以當地語系化方式保存，任何技術都可以用來指派文化特性 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="56aa1-151">如果應用程式已經為伺服器端 ASP.NET Core 建立了當地語系化配置，請繼續使用應用程式的現有當地語系化基礎結構，並在 :::no-loc(cookie)::: 應用程式的配置內設定當地語系化文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="56aa1-152">下列範例示範如何在中設定 :::no-loc(cookie)::: 可由當地語系化中介軟體讀取的目前文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="56aa1-153">在檔案的 :::no-loc(Razor)::: `Pages/_Host.cshtml` 開頭標記內立即建立運算式 `<body>` ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="56aa1-154">應用程式會以下列事件順序來處理當地語系化：</span><span class="sxs-lookup"><span data-stu-id="56aa1-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="56aa1-155">瀏覽器會將初始 HTTP 要求傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="56aa1-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="56aa1-156">文化特性是由當地語系化中介軟體所指派。</span><span class="sxs-lookup"><span data-stu-id="56aa1-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="56aa1-157">:::no-loc(Razor):::頁面中的運算式 `_Host` (`_Host.cshtml`) 會在中將文化特性保存 :::no-loc(cookie)::: 為回應的一部分。</span><span class="sxs-lookup"><span data-stu-id="56aa1-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="56aa1-158">瀏覽器會開啟 WebSocket 連接來建立互動式 :::no-loc(Blazor Server)::: 會話。</span><span class="sxs-lookup"><span data-stu-id="56aa1-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="56aa1-159">當地語系化中介軟體會讀取 :::no-loc(cookie)::: 並指派文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="56aa1-160">:::no-loc(Blazor Server):::會話的開頭是正確的文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

<span data-ttu-id="56aa1-161">使用時 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page> ，請使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> 屬性：</span><span class="sxs-lookup"><span data-stu-id="56aa1-161">When working with a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page>, use the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> property:</span></span>

```razor
@{
    this.Context.Response.:::no-loc(Cookie):::s.Append(
        :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
        :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="56aa1-162">提供 UI 以選擇文化特性</span><span class="sxs-lookup"><span data-stu-id="56aa1-162">Provide UI to choose the culture</span></span>

<span data-ttu-id="56aa1-163">若要提供 UI 讓使用者選取文化特性，建議使用以重新 *導向為基礎的方法* 。</span><span class="sxs-lookup"><span data-stu-id="56aa1-163">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="56aa1-164">此程式類似于當使用者嘗試存取安全資源時，web 應用程式中所發生的情況。</span><span class="sxs-lookup"><span data-stu-id="56aa1-164">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="56aa1-165">使用者會重新導向至登入頁面，然後重新導向回到原始資源。</span><span class="sxs-lookup"><span data-stu-id="56aa1-165">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="56aa1-166">應用程式會透過重新導向至控制器來保存使用者選取的文化特性。</span><span class="sxs-lookup"><span data-stu-id="56aa1-166">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="56aa1-167">控制器會將使用者的選取文化特性設定為 :::no-loc(cookie)::: ，並將使用者重新導向回原始的 URI。</span><span class="sxs-lookup"><span data-stu-id="56aa1-167">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="56aa1-168">在伺服器上建立 HTTP 端點，以設定使用者在中所選取的文化特性 :::no-loc(cookie)::: ，然後重新導向回原始的 URI：</span><span class="sxs-lookup"><span data-stu-id="56aa1-168">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="56aa1-169">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 動作結果來防止開啟重新導向攻擊。</span><span class="sxs-lookup"><span data-stu-id="56aa1-169">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="56aa1-170">如需詳細資訊，請參閱<xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="56aa1-170">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="56aa1-171">如果應用程式未設定為處理控制器動作：</span><span class="sxs-lookup"><span data-stu-id="56aa1-171">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="56aa1-172">將 MVC 服務新增至中的服務集合 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-172">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="56aa1-173">在下列情況中新增控制器端點路由 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="56aa1-173">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="56aa1-174">下列元件顯示如何在使用者選取文化特性時執行初始重新導向的範例：</span><span class="sxs-lookup"><span data-stu-id="56aa1-174">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="56aa1-175">其他資源</span><span class="sxs-lookup"><span data-stu-id="56aa1-175">Additional resources</span></span>

* <xref:fundamentals/localization>
