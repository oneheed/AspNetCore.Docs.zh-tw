---
title: ASP.NET Core Razor 元件類別庫
author: guardrex
description: 探索元件如何包含在 Blazor 外部元件程式庫的應用程式中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: fed5d26ecd73a4710ee794c413fd51e0b4cb4913
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280201"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="23f66-103">ASP.NET Core Razor 元件類別庫</span><span class="sxs-lookup"><span data-stu-id="23f66-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="23f66-104">您可以在類別庫中共用元件， [ Razor (RCL](xref:razor-pages/ui-class)跨專案) 。</span><span class="sxs-lookup"><span data-stu-id="23f66-104">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="23f66-105">*Razor 元件類別庫* 可以包含在：</span><span class="sxs-lookup"><span data-stu-id="23f66-105">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="23f66-106">方案中的另一個專案。</span><span class="sxs-lookup"><span data-stu-id="23f66-106">Another project in the solution.</span></span>
* <span data-ttu-id="23f66-107">NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="23f66-107">A NuGet package.</span></span>
* <span data-ttu-id="23f66-108">參考的 .NET 程式庫。</span><span class="sxs-lookup"><span data-stu-id="23f66-108">A referenced .NET library.</span></span>

<span data-ttu-id="23f66-109">如同元件是標準的 .NET 型別，RCL 所提供的元件是一般的 .NET 元件。</span><span class="sxs-lookup"><span data-stu-id="23f66-109">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="23f66-110">建立 RCL</span><span class="sxs-lookup"><span data-stu-id="23f66-110">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23f66-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23f66-111">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="23f66-112">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="23f66-112">Create a new project.</span></span>
1. <span data-ttu-id="23f66-113">選取 [ **Razor 類別庫**]。</span><span class="sxs-lookup"><span data-stu-id="23f66-113">Select **Razor Class Library**.</span></span> <span data-ttu-id="23f66-114">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="23f66-114">Select **Next**.</span></span>
1. <span data-ttu-id="23f66-115">在 [ **建立新的 Razor 類別庫** ] 對話方塊中，選取 [ **建立**]。</span><span class="sxs-lookup"><span data-stu-id="23f66-115">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="23f66-116">在 [專案名稱] 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="23f66-116">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="23f66-117">本主題中的範例會使用專案名稱 `ComponentLibrary` 。</span><span class="sxs-lookup"><span data-stu-id="23f66-117">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="23f66-118">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="23f66-118">Select **Create**.</span></span>
1. <span data-ttu-id="23f66-119">將 RCL 新增至方案：</span><span class="sxs-lookup"><span data-stu-id="23f66-119">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="23f66-120">以滑鼠右鍵按一下方案。</span><span class="sxs-lookup"><span data-stu-id="23f66-120">Right-click the solution.</span></span> <span data-ttu-id="23f66-121">選取 [**加入**  >  **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="23f66-121">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="23f66-122">流覽至 RCL 的專案檔。</span><span class="sxs-lookup"><span data-stu-id="23f66-122">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="23f66-123">選取 RCL 的專案檔 (`.csproj`) 。</span><span class="sxs-lookup"><span data-stu-id="23f66-123">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="23f66-124">從應用程式新增 RCL 的參考：</span><span class="sxs-lookup"><span data-stu-id="23f66-124">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="23f66-125">以滑鼠右鍵按一下應用程式專案。</span><span class="sxs-lookup"><span data-stu-id="23f66-125">Right-click the app project.</span></span> <span data-ttu-id="23f66-126">選取 [**加入**  >  **參考**]。</span><span class="sxs-lookup"><span data-stu-id="23f66-126">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="23f66-127">選取 RCL 專案。</span><span class="sxs-lookup"><span data-stu-id="23f66-127">Select the RCL project.</span></span> <span data-ttu-id="23f66-128">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="23f66-128">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="23f66-129">從範本產生 RCL 時，如果已選取 [ **支援頁面和流覽** 器] 核取方塊，則也請 `_Imports.razor` 使用下列內容將檔案新增至所產生專案的根目錄，以啟用 Razor 元件撰寫：</span><span class="sxs-lookup"><span data-stu-id="23f66-129">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="23f66-130">手動將檔案新增至所產生專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="23f66-130">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="23f66-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="23f66-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="23f66-132">使用 [ **Razor 類別庫**] 範本 (`razorclasslib`) 搭配 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的命令。</span><span class="sxs-lookup"><span data-stu-id="23f66-132">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="23f66-133">在下列範例中，會建立名為的 RCL `ComponentLibrary` 。</span><span class="sxs-lookup"><span data-stu-id="23f66-133">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="23f66-134">`ComponentLibrary`執行命令時，會自動建立保存的資料夾：</span><span class="sxs-lookup"><span data-stu-id="23f66-134">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="23f66-135">如果 `-s|--support-pages-and-views` 從範本產生 RCL 時使用參數，則也請 `_Imports.razor` 使用下列內容將檔案新增至所產生專案的根目錄，以啟用 Razor 元件撰寫：</span><span class="sxs-lookup"><span data-stu-id="23f66-135">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="23f66-136">手動將檔案新增至所產生專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="23f66-136">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="23f66-137">若要將程式庫新增至現有的專案，請 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 在命令列介面中使用命令。</span><span class="sxs-lookup"><span data-stu-id="23f66-137">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="23f66-138">在下列範例中，RCL 會新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="23f66-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="23f66-139">從應用程式的專案資料夾中，使用程式庫的路徑來執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="23f66-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="23f66-140">使用程式庫元件</span><span class="sxs-lookup"><span data-stu-id="23f66-140">Consume a library component</span></span>

<span data-ttu-id="23f66-141">若要使用在另一個專案的程式庫中定義的元件，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="23f66-141">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="23f66-142">使用完整類型名稱與命名空間。</span><span class="sxs-lookup"><span data-stu-id="23f66-142">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="23f66-143">使用 Razor 的指示詞 [`@using`](xref:mvc/views/razor#using) 。</span><span class="sxs-lookup"><span data-stu-id="23f66-143">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="23f66-144">您可以依名稱新增個別的元件。</span><span class="sxs-lookup"><span data-stu-id="23f66-144">Individual components can be added by name.</span></span>

<span data-ttu-id="23f66-145">在下列範例中， `ComponentLibrary` 是包含元件 () 的元件程式庫 `Component1` `Component1.razor` 。</span><span class="sxs-lookup"><span data-stu-id="23f66-145">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="23f66-146">`Component1`元件是 RCL 專案範本在建立程式庫時自動新增的範例元件。</span><span class="sxs-lookup"><span data-stu-id="23f66-146">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="23f66-147">`Component1`使用其命名空間參考元件：</span><span class="sxs-lookup"><span data-stu-id="23f66-147">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="23f66-148">或者，使用指示詞將程式庫帶入範圍中 [`@using`](xref:mvc/views/razor#using) ，並使用元件而不使用其命名空間：</span><span class="sxs-lookup"><span data-stu-id="23f66-148">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="23f66-149">（選擇性）在最上層檔案中加入指示詞， `@using ComponentLibrary` `_Import.razor` 讓程式庫的元件可供整個專案使用。</span><span class="sxs-lookup"><span data-stu-id="23f66-149">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="23f66-150">將指示詞加入至 `_Import.razor` 任何層級的檔案，以將命名空間套用至資料夾內的單一元件或元件集。</span><span class="sxs-lookup"><span data-stu-id="23f66-150">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="23f66-151">若為使用 [CSS 隔離](xref:blazor/components/css-isolation)的程式庫元件，則不需要在使用程式庫的應用程式中明確連結程式庫的個別元件樣式表單。</span><span class="sxs-lookup"><span data-stu-id="23f66-151">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="23f66-152">元件樣式會自動提供給取用應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="23f66-152">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="23f66-153">若要從程式庫資料夾的樣式表單中提供其他程式庫元件樣式 `wwwroot` ，請將取用應用程式檔案中的樣式表單連結 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 檔 (Blazor Server) ：</span><span class="sxs-lookup"><span data-stu-id="23f66-153">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="23f66-154">若要提供 `Component1` 的 `my-component` CSS 類別，請連結至應用程式檔案中程式庫的樣式表單 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 檔 (Blazor Server) ：</span><span class="sxs-lookup"><span data-stu-id="23f66-154">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="23f66-155">建立 Razor 具有靜態資產的元件類別庫</span><span class="sxs-lookup"><span data-stu-id="23f66-155">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="23f66-156">RCL 可以包含靜態資產。</span><span class="sxs-lookup"><span data-stu-id="23f66-156">An RCL can include static assets.</span></span> <span data-ttu-id="23f66-157">靜態資產可供任何使用程式庫的應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="23f66-157">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="23f66-158">如需詳細資訊，請參閱<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。</span><span class="sxs-lookup"><span data-stu-id="23f66-158">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-blazor-apps"></a><span data-ttu-id="23f66-159">將元件和靜態資產提供給多個託管 Blazor 應用程式</span><span class="sxs-lookup"><span data-stu-id="23f66-159">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="23f66-160">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="23f66-160">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-blazor-webassembly"></a><span data-ttu-id="23f66-161">的瀏覽器相容性分析器 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="23f66-161">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="23f66-162">Blazor WebAssembly 應用程式會以完整的 .NET API 介面區為目標，但由於瀏覽器沙箱條件約束，WebAssembly 並不支援所有的 .NET Api。</span><span class="sxs-lookup"><span data-stu-id="23f66-162">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="23f66-163"><xref:System.PlatformNotSupportedException>在 WebAssembly 上執行時，不支援的 api 會擲回。</span><span class="sxs-lookup"><span data-stu-id="23f66-163">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="23f66-164">當應用程式使用應用程式的目標平臺不支援的 Api 時，平臺相容性分析器會警告開發人員。</span><span class="sxs-lookup"><span data-stu-id="23f66-164">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="23f66-165">針對 Blazor WebAssembly 應用程式，這表示檢查瀏覽器中是否支援 api。</span><span class="sxs-lookup"><span data-stu-id="23f66-165">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="23f66-166">針對相容性分析器標注 .NET framework Api 是一項持續進行的程式，所以並非所有的 .NET framework API 目前都有批註。</span><span class="sxs-lookup"><span data-stu-id="23f66-166">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="23f66-167">Blazor WebAssembly 和 Razor 類別庫專案 *會* 藉由新增 `browser` 作為 MSBuild 專案的支援平臺，自動啟用瀏覽器相容性檢查 `SupportedPlatform` 。</span><span class="sxs-lookup"><span data-stu-id="23f66-167">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="23f66-168">程式庫開發人員可以手動將 `SupportedPlatform` 專案新增至程式庫的專案檔，以啟用此功能：</span><span class="sxs-lookup"><span data-stu-id="23f66-168">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="23f66-169">撰寫程式庫時，表示瀏覽器中不支援特定的 API，方法是指定 `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> ：</span><span class="sxs-lookup"><span data-stu-id="23f66-169">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="23f66-170">如需詳細資訊，請參閱將 [Api 標注為特定平臺上不支援 (dotnet/設計 GitHub 存放庫](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)。</span><span class="sxs-lookup"><span data-stu-id="23f66-170">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="23f66-171">Blazor JavaScript 隔離和物件參考</span><span class="sxs-lookup"><span data-stu-id="23f66-171">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="23f66-172">Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。</span><span class="sxs-lookup"><span data-stu-id="23f66-172">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="23f66-173">JavaScript 隔離提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="23f66-173">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="23f66-174">匯入的 JavaScript 不再干擾全域命名空間。</span><span class="sxs-lookup"><span data-stu-id="23f66-174">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="23f66-175">不需要程式庫和元件的取用者手動匯入相關的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="23f66-175">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="23f66-176">如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。</span><span class="sxs-lookup"><span data-stu-id="23f66-176">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="23f66-177">建立、封裝和寄送至 NuGet</span><span class="sxs-lookup"><span data-stu-id="23f66-177">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="23f66-178">由於元件程式庫是標準的 .NET 程式庫，因此封裝和傳送至 NuGet 與將任何程式庫封裝並傳送至 NuGet 並無不同。</span><span class="sxs-lookup"><span data-stu-id="23f66-178">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="23f66-179">封裝是使用命令 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) shell 中的命令來執行：</span><span class="sxs-lookup"><span data-stu-id="23f66-179">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="23f66-180">使用命令 shell 中的命令，將套件上傳至 NuGet [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 。</span><span class="sxs-lookup"><span data-stu-id="23f66-180">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="23f66-181">其他資源</span><span class="sxs-lookup"><span data-stu-id="23f66-181">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="23f66-182">將 XML 中繼語言 (IL) 修剪器設定檔新增至程式庫</span><span class="sxs-lookup"><span data-stu-id="23f66-182">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="23f66-183">類別庫的 CSS 隔離支援 Razor</span><span class="sxs-lookup"><span data-stu-id="23f66-183">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="23f66-184">將 XML 中繼語言 (IL) 連結器設定檔新增至程式庫</span><span class="sxs-lookup"><span data-stu-id="23f66-184">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
