---
title: 'ASP.NET Core :::no-loc(Blazor)::: CSS 隔離'
author: daveabrock
description: 瞭解 CSS 隔離如何讓您將 CSS 範圍設為您的元件，這可以簡化您的 CSS，並避免與其他元件或程式庫發生衝突。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 4fec0fa750b9209849030d0d6b7de8f4e163d62f
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570129"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="4c30b-103">ASP.NET Core :::no-loc(Blazor)::: CSS 隔離</span><span class="sxs-lookup"><span data-stu-id="4c30b-103">ASP.NET Core :::no-loc(Blazor)::: CSS isolation</span></span>

<span data-ttu-id="4c30b-104">依 [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="4c30b-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="4c30b-105">CSS 隔離會防止全域樣式的相依性，並協助避免元件和程式庫之間的樣式衝突，以簡化應用程式的 CSS 使用量。</span><span class="sxs-lookup"><span data-stu-id="4c30b-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="4c30b-106">啟用 CSS 隔離</span><span class="sxs-lookup"><span data-stu-id="4c30b-106">Enable CSS isolation</span></span> 

<span data-ttu-id="4c30b-107">若要定義元件特定樣式，請建立 `.razor.css` 符合元件檔案名的檔案 `.razor` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="4c30b-108">這個檔案 `.razor.css` 是限 *域的 CSS* 檔案。</span><span class="sxs-lookup"><span data-stu-id="4c30b-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="4c30b-109">若為 `MyComponent` 具有檔案的元件 `MyComponent.razor` ，請建立檔案與名為的元件 `MyComponent.razor.css` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="4c30b-110">`MyComponent`檔案名中的值 `.razor.css` **不** 區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="4c30b-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="4c30b-111">例如，若要 `Counter` 在預設專案範本中將 CSS 隔離新增至元件，請在檔案中 :::no-loc(Blazor)::: 加入名為的新檔案 `Counter.razor.css` `Counter.razor` ，然後新增下列 CSS：</span><span class="sxs-lookup"><span data-stu-id="4c30b-111">For example to add CSS isolation to the `Counter` component in the default :::no-loc(Blazor)::: project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="4c30b-112">中定義的樣式 `Counter.razor.css` 只會套用至元件的呈現輸出 `Counter` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="4c30b-113">在 `h1` 應用程式中其他位置定義的任何 CSS 宣告都不會與 `Counter` 樣式衝突。</span><span class="sxs-lookup"><span data-stu-id="4c30b-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="4c30b-114">為了確保在進行組合時進行樣式隔離， `@import` :::no-loc(Razor)::: 範圍的 CSS 檔案不支援區塊。</span><span class="sxs-lookup"><span data-stu-id="4c30b-114">In order to guarantee style isolation when bundling occurs, `@import` :::no-loc(Razor)::: blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="4c30b-115">CSS 隔離組合</span><span class="sxs-lookup"><span data-stu-id="4c30b-115">CSS isolation bundling</span></span>

<span data-ttu-id="4c30b-116">在組建期間發生 CSS 隔離。</span><span class="sxs-lookup"><span data-stu-id="4c30b-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="4c30b-117">在此程式期間，會 :::no-loc(Blazor)::: 重寫 CSS 選取器，以符合元件所呈現的標記。</span><span class="sxs-lookup"><span data-stu-id="4c30b-117">During this process, :::no-loc(Blazor)::: rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="4c30b-118">這些重寫的 CSS 樣式會以靜態資產的形式組合並產生 `{PROJECT NAME}.styles.css` ，其中預留位置 `{PROJECT NAME}` 是參考的封裝或產品名稱。</span><span class="sxs-lookup"><span data-stu-id="4c30b-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="4c30b-119">根據預設，會從應用程式的根路徑參考這些靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="4c30b-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="4c30b-120">在應用程式中，檢查 `<head>` 所產生之 HTML 標籤內的參考，以參考配套的檔案：</span><span class="sxs-lookup"><span data-stu-id="4c30b-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="4c30b-121">在配套的檔案中，每個元件都與一個範圍識別碼相關聯。</span><span class="sxs-lookup"><span data-stu-id="4c30b-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="4c30b-122">針對每個已設定樣式的元件，會附加格式的 HTML 屬性 `b-<10-character-string>` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="4c30b-123">每個應用程式的識別碼都是唯一的，而且不同。</span><span class="sxs-lookup"><span data-stu-id="4c30b-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="4c30b-124">在轉譯的 `Counter` 元件中，將 :::no-loc(Blazor)::: 範圍識別碼附加至 `h1` 元素：</span><span class="sxs-lookup"><span data-stu-id="4c30b-124">In the rendered `Counter` component, :::no-loc(Blazor)::: appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="4c30b-125">檔案會 `MyProjectName.styles.css` 使用範圍識別碼將樣式宣告與其元件組成群組。</span><span class="sxs-lookup"><span data-stu-id="4c30b-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="4c30b-126">下列範例提供上述元素的樣式 `<h1>` ：</span><span class="sxs-lookup"><span data-stu-id="4c30b-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="4c30b-127">在組建期間，會使用慣例建立專案組合 `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` ，其中預留位置 `{STATIC WEB ASSETS BASE PATH}` 是靜態 web 資產基底路徑。</span><span class="sxs-lookup"><span data-stu-id="4c30b-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="4c30b-128">如果使用其他專案（例如 NuGet 套件或[ :::no-loc(Razor)::: 類別庫](xref:blazor/components/class-libraries)），則配套的檔案如下：</span><span class="sxs-lookup"><span data-stu-id="4c30b-128">If other projects are utilized, such as NuGet packages or [:::no-loc(Razor)::: class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="4c30b-129">使用 CSS 匯入參考樣式。</span><span class="sxs-lookup"><span data-stu-id="4c30b-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="4c30b-130">未發佈為取用樣式之應用程式的靜態 web 資產。</span><span class="sxs-lookup"><span data-stu-id="4c30b-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="4c30b-131">子元件支援</span><span class="sxs-lookup"><span data-stu-id="4c30b-131">Child component support</span></span>

<span data-ttu-id="4c30b-132">CSS 隔離預設僅適用于您與格式相關聯的元件 `{COMPONENT NAME}.razor.css` ，其中預留位置 `{COMPONENT NAME}` 通常是元件名稱。</span><span class="sxs-lookup"><span data-stu-id="4c30b-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="4c30b-133">若要將變更套用至子元件，請使用 `::deep` 組合器至父元件檔案中的任何子代元素 `.razor.css` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="4c30b-134">`::deep`組合器會選取 *專案所* 產生之範圍識別碼子系的元素。</span><span class="sxs-lookup"><span data-stu-id="4c30b-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="4c30b-135">下列範例顯示使用稱為的子元件呼叫的父元件 `Parent` `Child` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="4c30b-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="4c30b-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="4c30b-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="4c30b-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="4c30b-138">`h1`使用組合器更新中的宣告 `Parent.razor.css` `::deep` ，以表示 `h1` 樣式宣告必須套用至父元件和其子系：</span><span class="sxs-lookup"><span data-stu-id="4c30b-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="4c30b-139">`h1`樣式現在適用于 `Parent` 和元件， `Child` 而不需要為子元件建立不同範圍的 CSS 檔案。</span><span class="sxs-lookup"><span data-stu-id="4c30b-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="4c30b-140">`::deep`組合器僅適用于子代元素。</span><span class="sxs-lookup"><span data-stu-id="4c30b-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="4c30b-141">下列 HTML 結構會 `h1` 如預期般將樣式套用至元件：</span><span class="sxs-lookup"><span data-stu-id="4c30b-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="4c30b-142">在此案例中，ASP.NET Core 會將父元件的範圍識別碼套用至專案 `div` ，讓瀏覽器知道要繼承父元件的樣式。</span><span class="sxs-lookup"><span data-stu-id="4c30b-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="4c30b-143">不過，排除專案 `div` 會移除子系關聯性，且樣式 **不** 會套用至子元件：</span><span class="sxs-lookup"><span data-stu-id="4c30b-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="4c30b-144">CSS 預處理器支援</span><span class="sxs-lookup"><span data-stu-id="4c30b-144">CSS preprocessor support</span></span>

<span data-ttu-id="4c30b-145">CSS 預處理器適用于利用變數、嵌套、模組、mixin 和繼承等功能來改善 CSS 開發。</span><span class="sxs-lookup"><span data-stu-id="4c30b-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="4c30b-146">雖然 CSS 隔離並不原生支援 CSS 預處理器（例如 Sass 或更少），但只要在 :::no-loc(Blazor)::: 建立程式期間重寫 css 選取器，就能整合 CSS 預處理器。</span><span class="sxs-lookup"><span data-stu-id="4c30b-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before :::no-loc(Blazor)::: rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="4c30b-147">例如，使用 Visual Studio，在 Visual Studio 的工作執行器瀏覽器中，將現有的預處理器編譯設定為 **之前的組建** 工作。</span><span class="sxs-lookup"><span data-stu-id="4c30b-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="4c30b-148">許多協力廠商 NuGet 套件（例如 [SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)）可以在進行 CSS 隔離之前，于組建程式的開頭編譯 SASS/SCSS 檔案，而且不需要額外的額外設定。</span><span class="sxs-lookup"><span data-stu-id="4c30b-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="4c30b-149">CSS 隔離設定</span><span class="sxs-lookup"><span data-stu-id="4c30b-149">CSS isolation configuration</span></span>

<span data-ttu-id="4c30b-150">CSS 隔離的設計是為了現成可用，但會提供一些 advanced 案例的設定，例如現有工具或工作流程的相依性。</span><span class="sxs-lookup"><span data-stu-id="4c30b-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="4c30b-151">自訂範圍識別碼格式</span><span class="sxs-lookup"><span data-stu-id="4c30b-151">Customize scope identifier format</span></span>

<span data-ttu-id="4c30b-152">根據預設，範圍識別碼會使用格式 `b-<10-character-string>` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="4c30b-153">若要自訂範圍識別碼格式，請將專案檔案更新為所需的模式：</span><span class="sxs-lookup"><span data-stu-id="4c30b-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="4c30b-154">在上述範例中，所產生的 CSS 會將 `MyComponent.:::no-loc(Razor):::.css` 其範圍識別碼從變更 `b-<10-character-string>` 為 `my-custom-scope-identifier` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-154">In the preceding example, the CSS generated for `MyComponent.:::no-loc(Razor):::.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="4c30b-155">變更靜態 web 資產的基底路徑</span><span class="sxs-lookup"><span data-stu-id="4c30b-155">Change base path for static web assets</span></span>

<span data-ttu-id="4c30b-156">檔案 `scoped.styles.css` 會在應用程式的根目錄產生。</span><span class="sxs-lookup"><span data-stu-id="4c30b-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="4c30b-157">在專案檔中，使用 `StaticWebAssetBasePath` 屬性來變更預設路徑。</span><span class="sxs-lookup"><span data-stu-id="4c30b-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="4c30b-158">下列範例會將檔案 `scoped.styles.css` 和應用程式資產的其餘部分放在 `_content` 路徑：</span><span class="sxs-lookup"><span data-stu-id="4c30b-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="4c30b-159">停用自動配套</span><span class="sxs-lookup"><span data-stu-id="4c30b-159">Disable automatic bundling</span></span>

<span data-ttu-id="4c30b-160">若選擇不 :::no-loc(Blazor)::: 在執行時間發行和載入範圍檔案的方式，請使用 `DisableScopedCssBundling` 屬性。</span><span class="sxs-lookup"><span data-stu-id="4c30b-160">To opt out of how :::no-loc(Blazor)::: publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="4c30b-161">使用這個屬性時，這表示其他工具或程式會負責從目錄中取得隔離的 CSS 檔案 `obj` ，並在執行時間進行發行和載入：</span><span class="sxs-lookup"><span data-stu-id="4c30b-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="4c30b-162">:::no-loc(Razor)::: 類別庫 (RCL) 支援</span><span class="sxs-lookup"><span data-stu-id="4c30b-162">:::no-loc(Razor)::: class library (RCL) support</span></span>

<span data-ttu-id="4c30b-163">當[ :::no-loc(Razor)::: 類別庫 (RCL) ](xref:razor-pages/ui-class)提供隔離的樣式時， `<link>` 標記的 `href` 屬性會指向 `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` ，其中預留位置為：</span><span class="sxs-lookup"><span data-stu-id="4c30b-163">When a [:::no-loc(Razor)::: class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="4c30b-164">`{STATIC WEB ASSET BASE PATH}`：靜態 web 資產基底路徑。</span><span class="sxs-lookup"><span data-stu-id="4c30b-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="4c30b-165">`{ASSEMBLY NAME}`：類別庫的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="4c30b-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="4c30b-166">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="4c30b-166">In the following example:</span></span>

* <span data-ttu-id="4c30b-167">靜態 web 資產基底路徑為 `_content/ClassLib` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="4c30b-168">類別庫的元件名稱為 `ClassLib` 。</span><span class="sxs-lookup"><span data-stu-id="4c30b-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="4c30b-169">如需 RCLs 和元件程式庫的詳細資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="4c30b-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="4c30b-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="4c30b-170"><xref:blazor/components/class-libraries>.</span></span>
