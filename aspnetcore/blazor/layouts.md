---
title: ASP.NET Core Blazor 版面配置
author: guardrex
description: 瞭解如何為應用程式建立可重複使用的版面配置元件 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: 9462b73ad67394e79de08e7d2b13bf6a3145a04e
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507963"
---
# <a name="aspnet-core-no-locblazor-layouts"></a>ASP.NET Core Blazor 版面配置

依 [Rainer Stropek](https://www.timecockpit.com) 和 [Luke Latham](https://github.com/guardrex)

某些應用程式專案（例如功能表、著作權訊息和公司標誌）通常是應用程式整體版面配置的一部分，且應用程式中的每個元件都會使用這些元素。 將這些專案的程式碼複製到應用程式的所有元件，並不是有效的方法。 每次其中一個元素需要更新時，都必須更新每個元件。 這類複製很難維護，而且可能會在一段時間後產生不一致的內容。 *版面* 配置會解決此問題。

技術上來說，版面配置只是另一個元件。 版面配置是在 Razor 範本或 c # 程式碼中定義，而且可以使用 [資料](xref:blazor/components/data-binding)系結、相依性 [插入](xref:blazor/fundamentals/dependency-injection)和其他元件案例。

若要將 *元件* 變成配置 *，元件* ：

* 繼承自 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> ，定義配置 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 內轉譯內容的屬性。
* 使用 Razor 語法 `@Body` 來指定配置標記中呈現內容的位置。

下列程式碼範例會顯示 Razor 版面配置元件的範本 `MainLayout.razor` 。 版面配置會繼承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 並設定 `@Body` 導覽列和頁尾之間的：

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a>`MainLayout` 元件

在以其中一個專案範本為基礎的應用程式中 Blazor ， `MainLayout` (`MainLayout.razor`) 元件位於應用程式的 `Shared` 資料夾中：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a>預設配置

在應用程式檔案的元件中指定預設的應用程式版面配置 <xref:Microsoft.AspNetCore.Components.Routing.Router> `App.razor` 。 下列 <xref:Microsoft.AspNetCore.Components.Routing.Router> 元件是由預設範本所提供，會 Blazor 將預設版面配置設定為 `MainLayout` 元件：

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

若要為內容提供預設版面配置 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> ，請 <xref:Microsoft.AspNetCore.Components.LayoutView> 指定 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 內容的：

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

如需元件的詳細資訊 <xref:Microsoft.AspNetCore.Components.Routing.Router> ，請參閱 <xref:blazor/fundamentals/routing> 。

將配置指定為路由器中的預設配置，是很有用的作法，因為它可以覆寫每個元件或每個資料夾。 偏好使用路由器來設定應用程式的預設版面配置，因為這是最常見的技巧。

## <a name="specify-a-layout-in-a-component"></a>指定元件中的版面配置

使用指示詞將版面配置套用 Razor `@layout` 至元件。 編譯器會將套用 `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> 至元件類別的轉換成。

下列元件的內容 `MasterList` 會插入到的 `MasterLayout` 位置 `@Body` ：

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

直接在元件中指定版面配置，會覆寫路由器中的 *預設* 組態集，或從匯入的指示詞 `@layout` `_Imports.razor` 。

## <a name="centralized-layout-selection"></a>集中式版面配置選取

應用程式的每個資料夾都可以選擇性地包含名為的範本檔案 `_Imports.razor` 。 編譯器包含在相同資料夾中所有範本的 imports 檔中所指定的指示詞 Razor ，並以遞迴方式在其所有子資料夾中包含這些指令程式。 因此， `_Imports.razor` 包含的檔案 `@layout MyCoolLayout` 可確保資料夾中的所有元件都使用 `MyCoolLayout` 。 不需要重複新增 `@layout MyCoolLayout` 至 `.razor` 資料夾和子資料夾內的所有檔案。 `@using` 指示詞也會以同樣的方式套用至元件。

下列檔案會匯 `_Imports.razor` 入：

* `MyCoolLayout`.
* Razor相同資料夾和任何子資料夾中的所有元件。
* `BlazorApp1.Data` 命名空間。
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

檔案 `_Imports.razor` 類似于 [ Razor views 和 pages 的 _ViewImports cshtml](xref:mvc/views/layout#importing-shared-directives) 檔案，但特別適用于 Razor 元件檔。

在中指定配置會 `_Imports.razor` 覆寫指定為路由器 *預設版面* 配置的版面配置。

> [!WARNING]
> 請勿 **not** 將指示詞新增 Razor `@layout` 至根檔案 `_Imports.razor` ，這會導致應用程式中的版面配置無限迴圈。 若要控制預設的應用程式版面配置，請在元件中指定版面配置 `Router` 。 如需詳細資訊，請參閱 [預設版面](#default-layout) 配置區段。

## <a name="nested-layouts"></a>嵌套版面配置

應用程式可由嵌套配置組成。 元件可以參考配置，進而參考另一個版面配置。 例如，使用嵌套配置來建立多層式功能表結構。

下列範例示範如何使用嵌套版面配置。 檔案 `EpisodesComponent.razor` 是要顯示的元件。 元件會參考 `MasterListLayout` ：

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

檔案會 `MasterListLayout.razor` 提供 `MasterListLayout` 。 版面配置會參考另一個版面配置， `MasterLayout` 也就是轉譯的位置。 `EpisodesComponent` 會呈現出現的位置 `@Body` ：

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

最後， `MasterLayout` 在中 `MasterLayout.razor` 包含最上層的版面配置元素，例如頁首、主功能表和頁尾。 `MasterListLayout` 使用 `EpisodesComponent` 呈現時，會 `@Body` 出現：

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a>Razor使用整合元件共用頁面配置

當可路由的元件整合到 Razor 頁面應用程式時，應用程式的共用配置可以與元件一起使用。 如需詳細資訊，請參閱<xref:blazor/components/prerendering-and-integration>。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/views/layout>
