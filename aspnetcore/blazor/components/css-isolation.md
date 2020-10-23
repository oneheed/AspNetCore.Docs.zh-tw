---
title: ASP.NET Core Blazor CSS 隔離
author: daveabrock
description: 瞭解 CSS 隔離如何讓您將 CSS 範圍設為您的元件，這可以簡化您的 CSS，並避免與其他元件或程式庫發生衝突。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: c154e746c4c88fc919b2c0dddaea5fd585427a82
ms.sourcegitcommit: d84a225ec3381355c343460deed50f2fa5722f60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92431835"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a>ASP.NET Core Blazor CSS 隔離

依 [Dave Brock](https://twitter.com/daveabrock)

CSS 隔離會防止全域樣式的相依性，並協助避免元件和程式庫之間的樣式衝突，以簡化應用程式的 CSS 使用量。

## <a name="enable-css-isolation"></a>啟用 CSS 隔離 

若要定義元件特定樣式，請建立 `razor.css` 符合元件檔案名的檔案 `.razor` 。 這個檔案 `razor.css` 是限 *域的 CSS*檔案。 

若為 `MyComponent` 具有檔案的元件 `MyComponent.razor` ，請建立檔案與名為的元件 `MyComponent.razor.css` 。 `MyComponent`檔案名中的值 `razor.css` **不**區分大小寫。

例如，若要 `Counter` 在預設專案範本中將 CSS 隔離新增至元件，請在檔案中 Blazor 加入名為的新檔案 `Counter.razor.css` `Counter.razor` ，然後新增下列 CSS：

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

中定義的樣式 `Counter.razor.css` 只會套用至元件的呈現輸出 `Counter` 。 在 `h1` 應用程式中其他位置定義的任何 CSS 宣告都不會與 `Counter` 樣式衝突。

> [!NOTE]
> 為了確保在進行組合時進行樣式隔離， `@import` Razor 範圍的 CSS 檔案不支援區塊。

## <a name="css-isolation-bundling"></a>CSS 隔離組合

在組建期間發生 CSS 隔離。 在此程式期間，會 Blazor 重寫 CSS 選取器，以符合元件所呈現的標記。 這些重寫的 CSS 樣式會以靜態資產的形式組合並產生 `{PROJECT NAME}.styles.css` ，其中預留位置 `{PROJECT NAME}` 是參考的封裝或產品名稱。

根據預設，會從應用程式的根路徑參考這些靜態檔案。 在應用程式中，檢查 `<head>` 所產生之 HTML 標籤內的參考，以參考配套的檔案：

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

在配套的檔案中，每個元件都與一個範圍識別碼相關聯。 針對每個已設定樣式的元件，會附加格式的 HTML 屬性 `b-<10-character-string>` 。 每個應用程式的識別碼都是唯一的，而且不同。 在轉譯的 `Counter` 元件中，將 Blazor 範圍識別碼附加至 `h1` 元素：

```html
<h1 b-3xxtam6d07>
```

檔案會 `MyProjectName.styles.css` 使用範圍識別碼將樣式宣告與其元件組成群組。 下列範例提供上述元素的樣式 `<h1>` ：

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

在組建期間，會使用慣例建立專案組合 `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` ，其中預留位置 `{STATIC WEB ASSETS BASE PATH}` 是靜態 web 資產基底路徑。

如果使用其他專案（例如 NuGet 套件或[ Razor 類別庫](xref:blazor/components/class-libraries)），則配套的檔案如下：

* 使用 CSS 匯入參考樣式。
* 未發佈為取用樣式之應用程式的靜態 web 資產。

## <a name="child-component-support"></a>子元件支援

CSS 隔離預設僅適用于您與格式相關聯的元件 `{COMPONENT NAME}.razor.css` ，其中預留位置 `{COMPONENT NAME}` 通常是元件名稱。 若要將變更套用至子元件，請使用 `::deep` 組合器至父元件檔案中的任何子代元素 `razor.css` 。 `::deep`組合器會選取*專案所*產生之範圍識別碼子系的元素。 

下列範例顯示使用稱為的子元件呼叫的父元件 `Parent` `Child` 。

`Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Child.razor`:

```razor
<h1>Child Component</h1>
```

`h1`使用組合器更新中的宣告 `Parent.razor.css` `::deep` ，以表示 `h1` 樣式宣告必須套用至父元件和其子系：

```css
::deep h1 { 
    color: red;
}
```

`h1`樣式現在適用于 `Parent` 和元件， `Child` 而不需要為子元件建立不同範圍的 CSS 檔案。

> [!NOTE]
> `::deep`組合器僅適用于子代元素。 下列 HTML 結構會 `h1` 如預期般將樣式套用至元件：
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> 在此案例中，ASP.NET Core 會將父元件的範圍識別碼套用至專案 `div` ，讓瀏覽器知道要繼承父元件的樣式。
>
> 不過，排除專案 `div` 會移除子系關聯性，且樣式 **不** 會套用至子元件：
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a>CSS 預處理器支援

CSS 預處理器適用于利用變數、嵌套、模組、mixin 和繼承等功能來改善 CSS 開發。 雖然 CSS 隔離並不原生支援 CSS 預處理器（例如 Sass 或更少），但只要在 Blazor 建立程式期間重寫 css 選取器，就能整合 CSS 預處理器。 例如，使用 Visual Studio，在 Visual Studio 的工作執行器瀏覽器中，將現有的預處理器編譯設定為 **之前的組建** 工作。

許多協力廠商 NuGet 套件（例如 [SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)）可以在進行 CSS 隔離之前，于組建程式的開頭編譯 SASS/SCSS 檔案，而且不需要額外的額外設定。

## <a name="css-isolation-configuration"></a>CSS 隔離設定

CSS 隔離的設計是為了現成可用，但會提供一些 advanced 案例的設定，例如現有工具或工作流程的相依性。

### <a name="customize-scope-identifier-format"></a>自訂範圍識別碼格式

根據預設，範圍識別碼會使用格式 `b-<10-character-string>` 。 若要自訂範圍識別碼格式，請將專案檔案更新為所需的模式：

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

在上述範例中，所產生的 CSS 會將 `MyComponent.Razor.css` 其範圍識別碼從變更 `b-<10-character-string>` 為 `my-custom-scope-identifier` 。

### <a name="change-base-path-for-static-web-assets"></a>變更靜態 web 資產的基底路徑

檔案 `scoped.styles.css` 會在應用程式的根目錄產生。 在專案檔中，使用 `StaticWebAssetBasePath` 屬性來變更預設路徑。 下列範例會將檔案 `scoped.styles.css` 和應用程式資產的其餘部分放在 `_content` 路徑：

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>停用自動配套

若選擇不 Blazor 在執行時間發行和載入範圍檔案的方式，請使用 `DisableScopedCssBundling` 屬性。 使用這個屬性時，這表示其他工具或程式會負責從目錄中取得隔離的 CSS 檔案 `obj` ，並在執行時間進行發行和載入：

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
