---
title: ASP.NET Core Razor 元件類別庫
author: guardrex
description: 探索元件如何包含在 Blazor 外部元件程式庫的應用程式中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
ms.openlocfilehash: f8e36cbe905b5ec2e674123c0f2ab6db99683c7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056409"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>ASP.NET Core Razor 元件類別庫

依 [Simon Timms](https://github.com/stimms)

您可以在類別庫中共用元件， [ Razor (RCL](xref:razor-pages/ui-class)跨專案) 。 *Razor 元件類別庫* 可以包含在：

* 方案中的另一個專案。
* NuGet 套件。
* 參考的 .NET 程式庫。

如同元件是標準的 .NET 型別，RCL 所提供的元件是一般的 .NET 元件。

## <a name="create-an-rcl"></a>建立 RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 建立新專案。
1. 選取 [ **Razor 類別庫** ]。 選取 [下一步]  。
1. 在 [ **建立新的 Razor 類別庫** ] 對話方塊中，選取 [ **建立** ]。
1. 在 [專案名稱]  欄位中提供專案名稱，或接受預設專案名稱。 本主題中的範例會使用專案名稱 `ComponentLibrary` 。 選取 [建立]。
1. 將 RCL 新增至方案：
   1. 以滑鼠右鍵按一下方案。 選取 [ **加入**  >  **現有專案** ]。
   1. 流覽至 RCL 的專案檔。
   1. 選取 RCL 的專案檔 (`.csproj`) 。
1. 從應用程式新增參考 RCL：
   1. 以滑鼠右鍵按一下應用程式專案。 選取 [ **加入**  >  **參考** ]。
   1. 選取 RCL 專案。 選取 [確定]  。

> [!NOTE]
> 從範本產生 RCL 時，如果已選取 [ **支援頁面和流覽** 器] 核取方塊，則也請 `_Imports.razor` 使用下列內容將檔案新增至所產生專案的根目錄，以啟用 Razor 元件撰寫：
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> 手動將檔案新增至所產生專案的根目錄。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 使用 [ **Razor 類別庫** ] 範本 (`razorclasslib`) 搭配 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的命令。 在下列範例中，會建立名為的 RCL `ComponentLibrary` 。 `ComponentLibrary`執行命令時，會自動建立保存的資料夾：

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > 如果 `-s|--support-pages-and-views` 從範本產生 RCL 時使用參數，則也請 `_Imports.razor` 使用下列內容將檔案新增至所產生專案的根目錄，以啟用 Razor 元件撰寫：
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > 手動將檔案新增至所產生專案的根目錄。

1. 若要將程式庫新增至現有的專案，請 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 在命令列介面中使用命令。 在下列範例中，RCL 會新增至應用程式。 從應用程式的專案資料夾中，使用程式庫的路徑來執行下列命令：

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>使用程式庫元件

若要使用在另一個專案的程式庫中定義的元件，請使用下列其中一種方法：

* 使用完整類型名稱與命名空間。
* 使用 Razor 的指示詞 [`@using`](xref:mvc/views/razor#using) 。 您可以依名稱新增個別的元件。

在下列範例中， `ComponentLibrary` 是包含元件 () 的元件程式庫 `Component1` `Component1.razor` 。 `Component1`元件是 RCL 專案範本在建立程式庫時自動新增的範例元件。

`Component1`使用其命名空間參考元件：

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

或者，使用指示詞將程式庫帶入範圍中 [`@using`](xref:mvc/views/razor#using) ，並使用元件而不使用其命名空間：

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

（選擇性）在最上層檔案中加入指示詞， `@using ComponentLibrary` `_Import.razor` 讓程式庫的元件可供整個專案使用。 將指示詞加入至 `_Import.razor` 任何層級的檔案，以將命名空間套用至資料夾內的單一元件或元件集。

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

若要提供 `Component1` 的 `my-component` CSS 類別，請連結至應用程式檔案中程式庫的樣式表單 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 檔 (Blazor Server) ：

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>建立 Razor 具有靜態資產的元件類別庫

RCL 可以包含靜態資產。 靜態資產可供任何使用程式庫的應用程式使用。 如需詳細資訊，請參閱<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>將元件和靜態資產提供給多個託管 Blazor 應用程式

如需詳細資訊，請參閱<xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>。

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>的瀏覽器相容性分析器 Blazor WebAssembly

Blazor WebAssembly 應用程式會以完整的 .NET API 介面區為目標，但由於瀏覽器沙箱條件約束，WebAssembly 並不支援所有的 .NET Api。 <xref:System.PlatformNotSupportedException>在 WebAssembly 上執行時，不支援的 api 會擲回。 當應用程式使用應用程式的目標平臺不支援的 Api 時，平臺相容性分析器會警告開發人員。 針對 Blazor WebAssembly 應用程式，這表示檢查瀏覽器中是否支援 api。 針對相容性分析器標注 .NET framework Api 是一項持續進行的程式，所以並非所有的 .NET framework API 目前都有批註。

Blazor WebAssembly 和 Razor 類別庫專案 *會* 藉由新增 `browser` 作為 MSBuild 專案的支援平臺，自動啟用瀏覽器相容性檢查 `SupportedPlatform` 。 程式庫開發人員可以手動將 `SupportedPlatform` 專案新增至程式庫的專案檔，以啟用此功能：

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

撰寫程式庫時，表示瀏覽器中不支援特定的 API，方法是指定 `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> ：

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

如需詳細資訊，請參閱將 [Api 標注為特定平臺上不支援 (dotnet/設計 GitHub 存放庫](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)。

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 隔離和物件參考

Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。 JavaScript 隔離提供下列優點：

* 匯入的 JavaScript 不再干擾全域命名空間。
* 不需要程式庫和元件的取用者手動匯入相關的 JavaScript。

如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>建立、封裝和寄送至 NuGet

由於元件程式庫是標準的 .NET 程式庫，因此封裝和傳送至 NuGet 與將任何程式庫封裝並傳送至 NuGet 並無不同。 封裝是使用命令 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) shell 中的命令來執行：

```dotnetcli
dotnet pack
```

使用命令 shell 中的命令，將套件上傳至 NuGet [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 。

## <a name="additional-resources"></a>其他資源

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [將 XML 中繼語言 (IL) 修剪器設定檔新增至程式庫](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [將 XML 中繼語言 (IL) 連結器設定檔新增至程式庫](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
