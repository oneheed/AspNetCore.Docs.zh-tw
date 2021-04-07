---
title: Razor從類別庫取用 ASP.NET Core 元件 Razor
author: guardrex
description: 探索元件如何包含在 Blazor 外部類別庫的應用程式中 Razor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2021
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
ms.openlocfilehash: 02c5edeaaac97cefac8a39279fd4b6644a1535ab
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106563933"
---
# <a name="consume-aspnet-core-razor-components-from-razor-class-libraries"></a>Razor從類別庫取用 ASP.NET Core 元件 Razor

您可以在類別庫中共用元件， [ Razor (RCL](xref:razor-pages/ui-class)跨專案) 。 在應用程式中包含元件和靜態資產，來源：

* 方案中的另一個專案。
* 參考的 .NET 程式庫。
* NuGet 套件。

如同元件是標準的 .NET 型別，RCL 所提供的元件是一般的 .NET 元件。

## <a name="create-an-rcl"></a>建立 RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 建立新專案。
1. 在 [**建立新專案**] 對話方塊中，從 ASP.NET Core 專案範本清單中選取 [ **Razor 類別庫**]。 選取 [下一步] 。
1. 在 [ **設定您的新專案** ] 對話方塊中，于 [ **專案名稱** ] 欄位中提供專案名稱，或接受預設專案名稱。 本主題中的範例會使用專案名稱 `ComponentLibrary` 。 選取 [建立]  。
1. 在 [ **建立新的 Razor 類別庫** ] 對話方塊中，選取 [ **建立**]。
1. 將 RCL 新增至方案：
   1. 開啟解決方案。
   1. 以滑鼠右鍵按一下 **方案總管** 中的方案。 選取 [**加入**  >  **現有專案**]。
   1. 流覽至 RCL 的專案檔。
   1. 選取 RCL 的專案檔 (`.csproj`) 。
1. 從應用程式新增 RCL 的參考：
   1. 以滑鼠右鍵按一下應用程式專案。 選取 [**加入**  >  **專案參考**]。
   1. 選取 RCL 專案。 選取 [確定]。

::: moniker range=">= aspnetcore-5.0"

如果在從範本產生 RCL 時，已選取 [ **支援頁面和流覽** 器] 核取方塊以支援頁面和瀏覽器：

* `_Imports.razor`使用下列內容將檔案新增至產生的 RCL 專案的根目錄，以啟用 Razor 元件撰寫：

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* 將下列 `SupportedPlatform` 專案新增至專案檔 (`.csproj`) ：

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  如需專案的詳細資訊 `SupportedPlatform` ，請參閱的[瀏覽器相容 Blazor WebAssembly 性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)一節。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

從範本產生 RCL 時，如果已選取 [ **支援頁面和流覽** 器] 核取方塊以支援頁面和視圖，請 `_Imports.razor` 使用下列內容將檔案新增至所產生 RCL 專案的根目錄，以啟用 Razor 元件撰寫：

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 建立新專案。
1. 在 [ **Web 和主控台**] 底下的提要欄位中，選取 [ **應用程式**]。 在 [ **ASP.NET Core**] 下，從顯示的專案範本中選取 [ **Razor 類別庫**]。 選取 [下一步] 。
1. 使用 [ **目標 framework** ] 下拉式清單選取程式庫的目標 framework。 選取 [下一步] 。
1. 在 [ **設定您的新類別庫** ] 對話方塊中，于 [ **專案名稱** ] 欄位中提供專案名稱。 本主題中的範例會使用專案名稱 `ComponentLibrary` 。 選取 [建立]  。
1. 將 RCL 新增至方案：
   1. 開啟解決方案。
   1. 以滑鼠右鍵按一下 **方案總管** 中的方案。 選取 [**加入**  >  **現有專案**]。
   1. 流覽至 RCL 的專案檔。
   1. 選取 RCL 的專案檔 (`.csproj`) 。
1. 從應用程式新增 RCL 的參考：
   1. 以滑鼠右鍵按一下應用程式專案。 選取 [**加入**  >  **參考**]。
   1. 選取 RCL 專案。 選取 [確定]。

::: moniker range=">= aspnetcore-5.0"

如果在從範本產生 RCL 時，已選取 [ **支援頁面和流覽** 器] 核取方塊以支援頁面和瀏覽器：

* `_Imports.razor`使用下列內容將檔案新增至產生的 RCL 專案的根目錄，以啟用 Razor 元件撰寫：

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* 將下列 `SupportedPlatform` 專案新增至專案檔 (`.csproj`) ：

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  如需專案的詳細資訊 `SupportedPlatform` ，請參閱的[瀏覽器相容 Blazor WebAssembly 性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)一節。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

從範本產生 RCL 時，如果已選取 [ **支援頁面和流覽** 器] 核取方塊以支援頁面和視圖，請 `_Imports.razor` 使用下列內容將檔案新增至所產生 RCL 專案的根目錄，以啟用 Razor 元件撰寫：

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

1. 使用 [ **Razor 類別庫**] 專案範本 (`razorclasslib`) 搭配 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的命令。 在下列範例中，會 `ComponentLibrary` 使用選項來建立並命名 RCL `-o|--output` 。 `ComponentLibrary`執行命令時，會自動建立保存的資料夾：

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

1. 若要將程式庫新增至現有的專案，請 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 在命令列介面中使用命令。 在下列命令中， `{PATH TO LIBRARY}` 預留位置是程式庫專案資料夾的路徑：

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

::: moniker range=">= aspnetcore-5.0"

如果 `-s|--support-pages-and-views` 從範本產生 RCL 時，使用選項來支援頁面和瀏覽器：

* `_Imports.razor`使用下列內容將檔案新增至產生的 RCL 專案的根目錄，以啟用 Razor 元件撰寫：

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* 將下列 `SupportedPlatform` 專案新增至專案檔 (`.csproj`) ：

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  如需專案的詳細資訊 `SupportedPlatform` ，請參閱的[瀏覽器相容 Blazor WebAssembly 性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)一節。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

如果 `-s|--support-pages-and-views` 從範本產生 RCL 時，使用此選項來支援頁面和瀏覽器，請 `_Imports.razor` 使用下列內容將檔案新增至所產生 RCL 專案的根目錄，以啟用 Razor 元件撰寫：

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

---

## <a name="consume-a-razor-component-from-an-rcl"></a>使用 Razor RCL 中的元件

若要在另一個專案中使用 RCL 的元件，請使用下列其中一種方法：

* 使用完整元件類型名稱，其中包含 RCL 的命名空間。
* 如果的指示詞宣告 Razor [`@using`](xref:mvc/views/razor#using) RCL 的命名空間，則可以使用名稱來新增個別元件，而不需要 RCL 的命名空間。 使用下列方法：
  * 將指示詞加入 `@using` 至個別的元件。
  * 將指示詞包含 `@using` 在最上層的檔案中，以將連結 `_Imports.razor` 庫的元件提供給整個專案。 將指示詞加入至 `_Imports.razor` 任何層級的檔案，以將命名空間套用至資料夾內的單一元件或元件集。 使用檔案時 `_Imports.razor` ，個別的元件不需要 `@using` RCL 命名空間的指示詞。

在下列範例中， `ComponentLibrary` 是包含元件的 RCL `Component1` 。 `Component1`元件是一個範例元件，會自動新增至從 RCL 專案範本建立的 RCL，而不是為了支援頁面和視圖而建立。

> [!NOTE]
> 如果建立 RCL 以支援頁面和瀏覽器， `Component1` 如果您打算遵循本文中的範例，請手動將元件和其靜態資產新增至 RCL。 此區段會顯示元件和靜態資產。

`Component1.razor` 在 `ComponentLibrary` RCL 中：

```razor
<div class="my-component">
    This component is defined in the <strong>ComponentLibrary</strong> package.
</div>
```

在使用 RCL 的應用程式中， `Component1` 使用其命名空間參考元件，如下列範例所示。

`Pages/ConsumeComponent1.razor`:

```razor
@page "/consume-component-1"

<h1>Consume component (full namespace example)</h1>

<ComponentLibrary.Component1 />
```

或者，加入指示詞 [`@using`](xref:mvc/views/razor#using) ，並使用元件，但不包含其命名空間。 下列指示詞 `@using` 也可以出現在 `_Imports.razor` 目前資料夾或上方的任何檔案中。

`Pages/ConsumeComponent2.razor`:

```razor
@page "/consume-component-2"
@using ComponentLibrary

<h1>Consume component (<code>@@using</code> example)</h1>

<Component1 />
```

::: moniker range=">= aspnetcore-5.0"

若為使用 [CSS 隔離](xref:blazor/components/css-isolation)的程式庫元件，則會自動將元件樣式提供給取用應用程式使用。 在使用程式庫的應用程式中，不需要連結程式庫的個別元件樣式表單。 在上述範例中， `Component1` `Component1.razor.css` 會自動包含 () 的樣式表單。

`Component1.razor.css` 在 `ComponentLibrary` RCL 中：

```css
.my-component {
    border: 2px dashed red;
    padding: 1em;
    margin: 1em 0;
    background-image: url('background.png');
}
```

背景影像也會包含在 RCL 專案範本中，並位於 RCL 的 `wwwroot` 資料夾中。

`wwwroot/background.png` 在 `ComponentLibrary` RCL 中：

![RCL 專案範本中的對角線背景影像](class-libraries/_static/background.png)

::: moniker-end

::: moniker range=">= aspnetcore-6.0"

若要從程式庫資料夾的樣式表單中提供其他程式庫元件樣式 `wwwroot` ，請使用架構的元件連結樣式表單 `Link` 。

下一個範例會使用下列背景影像。 如果您執行本節所示的範例，請在映射上按一下滑鼠右鍵，將它儲存在本機。

`wwwroot/extra-background.png` 在 `ComponentLibrary` RCL 中：

![由開發人員新增至程式庫的對角線式背景影像](class-libraries/_static/extra-background.png)

使用類別，將新的樣式表單加入至 RCL `extra-style` 。

`wwwroot/additionalStyles.css` 在 `ComponentLibrary` RCL 中：

```css
.extra-style {
    border: 2px dashed blue;
    padding: 1em;
    margin: 1em 0;
    background-image: url('extra-background.png');
}
```

將元件加入至使用類別的 RCL 中 `extra-style` 。

`ExtraStyles.razor` 在 `ComponentLibrary` RCL 中：

```razor
<Link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />

<div class="extra-style">
    <p>
        This component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

將頁面新增至應用程式，該應用程式會使用 `ExtraStyles` RCL 中的元件。

`Pages/ConsumeComponent3.razor`:

```razor
@page "/consume-component-3"
@using ComponentLibrary

<h1>Consume component (<code>additionalStyles.css</code> example)</h1>

<ExtraStyles />
```

當 `Link` 元件在子元件中使用時，如果轉譯具有元件的子系，則父元件的任何其他子元件也可以使用連結的資產 `Link` 。

使用元件的替代方法 `Link` 是連結至應用程式標記中程式庫的樣式表單 `<head>` 。

`wwwroot/index.html` 檔案 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 檔 (Blazor Server) ：

```diff
+ <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
```

在子元件中使用 `Link` 元件並將 `<link>` HTML 標籤放在或中的差異 `wwwroot/index.html` 在於 `Pages/_Host.cshtml` 架構元件的轉譯 html 標記：

* 可依應用程式狀態修改。 `<link>`應用程式狀態無法修改硬式編碼的 HTML 標籤。
* 當父元件不再呈現時，會從 HTML 中移除 `<head>` 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

RCL 的範例元件使用下列背景影像和樣式表單 `Component1` 。 不需要將這些靜態資產新增至從 RCL 專案範本建立的新 RCL，因為它們是由專案範本自動新增的。

`wwwroot/background.png` 在 `ComponentLibrary` RCL 中：

![RCL 專案範本新增至程式庫的對角線式背景影像](class-libraries/_static/background.png)

`wwwroot/styles.css` 在 `ComponentLibrary` RCL 中：

```css
.my-component {
    border: 2px dashed red;
    padding: 1em;
    margin: 1em 0;
    background-image: url('background.png');
}
```

若要提供 `Component1` 的 `my-component` CSS 類別，請連結至應用程式標記中程式庫的樣式表單 `<head>` 。

`wwwroot/index.html` 檔案 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 檔 (Blazor Server) ：

```diff
+ <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
```

::: moniker-end

## <a name="create-an-rcl-with-static-assets"></a>建立具有靜態資產的 RCL

使用程式庫的任何應用程式都可以使用 RCL 的靜態資產。

將靜態資產放在 `wwwroot` RCL 的資料夾中，並在應用程式中使用下列路徑來參考靜態資產： `_content/{LIBRARY NAME}/{ASSET FILE NAME}` 。 `{LIBRARY NAME}`預留位置是程式庫名稱。 `{ASSET FILE NAME}`預留位置是檔案名。

下列範例示範如何使用 RCL 靜態資產搭配名為的 RCL `ComponentLibrary` ，以及使用 Blazor RCL 的應用程式。 應用程式有 RCL 的專案參考 `ComponentLibrary` 。

&reg;本章節的範例會使用下列 Jeep 映射。 如果您執行本節所示的範例，請在映射上按一下滑鼠右鍵，將它儲存在本機。

`wwwroot/jeep-yj.png` 在 `ComponentLibrary` RCL 中：

![Jeep YJ&reg;](class-libraries/_static/jeep-yj.png)

將下列 `JeepYJ` 元件新增至 RCL。

`JeepYJ.razor` 在 `ComponentLibrary` RCL 中：

```razor
<h3>ComponentLibrary.JeepYJ</h3>

<p>
    <img alt="Jeep YJ&reg;" src="_content/ComponentLibrary/jeep-yj.png" />
</p>
```

將下列 `Jeep` 元件新增至使用 RCL 的應用程式 `ComponentLibrary` 。 `Jeep`元件使用：

* RCL 資料夾中的 Jeep 的 YJ &reg; 映射 `ComponentLibrary` `wwwroot` 。
* `JeepYJ`來自 RCL 的元件。

`Pages/Jeep.razor`:

```razor
@page "/jeep"
@using ComponentLibrary

<div style="float:left;margin-right:10px">
    <h3>Direct use</h3>

    <p>
        <img alt="Jeep YJ&reg;" src="_content/ComponentLibrary/jeep-yj.png" />
    </p>
</div>

<JeepYJ />

<p>
    <em>Jeep</em> and <em>Jeep YJ</em> are registered trademarks of 
    <a href="https://www.stellantis.com">FCA US LLC (Stellantis NV)</a>.
</p>
```

轉譯的 `Jeep` 元件：

![Jeep 元件](class-libraries/_static/jeep-component.png)

如需詳細資訊，請參閱<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。

## <a name="supply-components-and-static-assets-to-multiple-hosted-blazor-apps"></a>將元件和靜態資產提供給多個託管 Blazor 應用程式

如需詳細資訊，請參閱<xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries-for-multiple-blazor-webassembly-apps>。

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-blazor-webassembly"></a>的瀏覽器相容性分析器 Blazor WebAssembly

Blazor WebAssembly 應用程式會以完整的 .NET API 介面區為目標，但由於瀏覽器沙箱條件約束，WebAssembly 並不支援所有的 .NET Api。 <xref:System.PlatformNotSupportedException>在 WebAssembly 上執行時，不支援的 api 會擲回。 當應用程式使用應用程式的目標平臺不支援的 Api 時，平臺相容性分析器會警告開發人員。 針對 Blazor WebAssembly 應用程式，這表示檢查瀏覽器中是否支援 api。 針對相容性分析器標注 .NET framework Api 是一項持續進行的程式，所以並非所有的 .NET framework API 目前都有批註。

Blazor WebAssembly 和 RCL projects *會自動* 啟用瀏覽器相容性檢查，方法是新增 `browser` 為具有 MSBuild 專案的支援平臺 `SupportedPlatform` 。 程式庫開發人員可以手動將 `SupportedPlatform` 專案新增至程式庫的專案檔，以啟用此功能：

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

## <a name="blazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 隔離和物件參考

Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。 JavaScript 隔離提供下列優點：

* 匯入的 JavaScript 不再干擾全域命名空間。
* 不需要程式庫和元件的取用者手動匯入相關的 JavaScript。

如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>建立、封裝和寄送至 NuGet

因為 Razor 包含元件的類別庫 Razor 是標準的 .net 程式庫，所以將它們封裝並傳送至 nuget 與封裝和將任何程式庫傳送至 nuget 並無不同。 封裝是使用命令 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) shell 中的命令來執行：

```dotnetcli
dotnet pack
```

使用命令 shell 中的命令，將套件上傳至 NuGet [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 。

## <a name="trademarks"></a>商標

*Jeep* 和 *Jeep YJ* 是 [FCA US LLC (Stellantis NV)](https://www.stellantis.com)的注冊商標。

## <a name="additional-resources"></a>其他資源

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [將 XML 中繼語言 (IL) 修剪器設定檔新增至程式庫](xref:blazor/host-and-deploy/configure-trimmer)
* [類別庫的 CSS 隔離支援 Razor](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [將 XML 中繼語言 (IL) 連結器設定檔新增至程式庫](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
