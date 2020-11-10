---
title: ASP.NET Core 3.1 的新功能
author: rick-anderson
description: 瞭解 ASP.NET Core 3.1 中的新功能。
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: dd012a2104f574865ed577ab3c0e81dc9cc9596d
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431013"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3.1 的新功能

本文將重點說明 ASP.NET Core 3.1 中最重要的變更，以及相關檔的連結。

## <a name="partial-class-support-for-no-locrazor-components"></a>元件的部分類別支援 Razor

Razor 元件現在會以部分類別的形式產生。 您 Razor 可以使用定義為部分類別的程式碼後端檔案來撰寫元件的程式碼，而不是在單一檔案中定義元件的所有程式碼。 如需詳細資訊，請參閱 [部分類別支援](xref:blazor/components/index#partial-class-support)。

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor 元件標記協助程式並將參數傳遞給最上層元件

在 Blazor ASP.NET Core 3.0 中，元件是使用 HTML 協助程式 () 轉譯成頁面和瀏覽器 `Html.RenderComponentAsync` 。 在 ASP.NET Core 3.1 中，使用新的元件標記協助程式從頁面或視圖轉譯元件：

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

ASP.NET Core 3.1 仍支援 HTML Helper，但建議使用元件標記協助程式。

Blazor Server 應用程式現在可以在初始轉譯期間，將參數傳遞至最上層元件。 之前，您只能使用 [RenderMode](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)將參數傳遞至最上層元件。 在此版本中，支援 [RenderMode](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) 和 [RenderMode](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) 。 任何指定的參數值會序列化為 JSON，並包含在初始回應中。

例如， `Counter` 具有 () 遞增數量的元件 `IncrementAmount` ：

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

如需詳細資訊，請參閱將 [元件整合至 Razor 頁面和 MVC 應用程式](xref:blazor/components/prerendering-and-integration)。

## <a name="support-for-shared-queues-in-httpsys"></a>支援 HTTP.sys 中的共用佇列

[HTTP.sys](xref:fundamentals/servers/httpsys) 支援建立匿名要求佇列。 在 ASP.NET Core 3.1 中，我們已新增建立或附加至現有命名 HTTP.sys 要求佇列的功能。 建立或附加至現有的已命名 HTTP.sys 要求佇列，可讓擁有佇列 HTTP.sys 控制器進程與接聽程式進程無關的情況。 這種獨立性讓您能夠在接聽程式進程重新開機之間保留現有的連接和排入佇列的要求：

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>SameSite s 的重大變更 cookie

SameSite s 的行為 cookie 已變更，以反映即將進行的瀏覽器變更。 這可能會影響 AzureAd、OpenIdConnect 或 WsFederation 等驗證案例。 如需詳細資訊，請參閱<xref:security/samesite>。

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>防止應用程式中事件的預設動作 Blazor

使用指示詞 `@on{EVENT}:preventDefault` 屬性以防止事件的預設動作。 在下列範例中，會防止在文字方塊中顯示金鑰字元的預設動作：

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

如需詳細資訊，請參閱 [防止預設動作](xref:blazor/components/event-handling#prevent-default-actions)。

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>停止應用程式中的事件傳播 Blazor

使用指示詞 `@on{EVENT}:stopPropagation` 屬性停止事件傳播。 在下列範例中，選取此核取方塊可防止從子系的 click 事件 `<div>` 傳播至父系 `<div>` ：

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

如需詳細資訊，請參閱 [停止事件傳播](xref:blazor/components/event-handling#stop-event-propagation)。

## <a name="detailed-errors-during-no-locblazor-app-development"></a>應用程式開發期間的詳細錯誤 Blazor

當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。 發生錯誤時， Blazor 應用程式會在畫面底部顯示金色橫條：

* 在開發期間，金線會將您導向瀏覽器主控台，您可以在其中查看例外狀況。
* 在生產環境中，金級橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。

如需詳細資訊，請參閱 [開發期間的詳細錯誤](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)。
