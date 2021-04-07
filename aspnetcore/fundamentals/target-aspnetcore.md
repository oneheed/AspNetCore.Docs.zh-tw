---
title: 在類別庫中使用 ASP.NET Core Api
author: rick-anderson
description: 瞭解如何在類別庫中使用 ASP.NET Core Api。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 8e220efdcb9a071adfc3a8a57e3a221cd9e01dd3
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564272"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>在類別庫中使用 ASP.NET Core Api

作者：[Scott Addie](https://github.com/scottaddie)

本檔提供在類別庫中使用 ASP.NET Core Api 的指引。 如需其他程式庫的指引，請參閱 [開放原始碼程式庫指引](/dotnet/standard/library-guidance/)。

## <a name="determine-which-aspnet-core-versions-to-support"></a>判斷要支援哪些 ASP.NET Core 版本

ASP.NET Core 遵守 [.Net Core 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。 判斷要在程式庫中支援哪些 ASP.NET Core 版本時，請參閱支援原則。 程式庫應：

* 請努力支援所有分類為 *長期支援* (LTS) 的 ASP.NET Core 版本。
* 不會受到支援 ASP.NET Core 版本分類為) *的生命週期結束* (。

當 ASP.NET Core 的預覽版本可供使用時，會將重大變更張貼在 [aspnet/公告](https://github.com/aspnet/Announcements/issues) GitHub 存放庫中。 您可以在開發架構功能時進行程式庫的相容性測試。

## <a name="use-the-aspnet-core-shared-framework"></a>使用 ASP.NET Core 共用架構

隨著 .NET Core 3.0 的發行，許多 ASP.NET Core 元件不再以套件的形式發佈至 NuGet。 相反地，這些元件會包含在 `Microsoft.AspNetCore.App` 與 .NET Core SDK 和執行時間安裝程式一起安裝的共用架構中。 如需不再發佈的套件清單，請參閱 [移除淘汰的封裝參考](xref:migration/22-to-30#remove-obsolete-package-references)。

從 .NET Core 3.0，使用 MSBuild SDK 的專案會 `Microsoft.NET.Sdk.Web` 隱含地參考共用架構。 使用 `Microsoft.NET.Sdk` 或 SDK 的專案 `Microsoft.NET.Sdk.Razor` 必須參考 ASP.NET Core，才能在共用架構中使用 ASP.NET Core api。

若要參考 ASP.NET Core，請將下列 `<FrameworkReference>` 元素新增至您的專案檔：

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

以這種方式參考 ASP.NET Core 僅支援以 .NET Core 3.x 為目標的專案。

## <a name="include-blazor-extensibility"></a>包含 Blazor 擴充性

Blazor 支援 WebAssembly (WASM) 和以伺服器為基礎的 [裝載模型](xref:blazor/hosting-models)。 除非有特定原因不支援這兩個裝載模型，否則[ Razor 元件](xref:blazor/components/index)程式庫應同時支援這兩種裝載模型。 Razor元件程式庫必須使用[Microsoft .Net. Sdk Razor 。SDK](xref:razor-pages/sdk)。

### <a name="support-both-hosting-models"></a>支援兩種裝載模型

若要支援 Razor 和專案的元件耗用量 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) [Blazor Server](xref:blazor/hosting-models#blazor-server) ，請針對您的編輯器使用下列指示。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

使用 [ **Razor 類別庫**] 專案範本。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在整合式終端機中執行下列命令：

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

使用 [ **Razor 類別庫**] 專案範本。

---

::: moniker range=">= aspnetcore-5.0"

從專案範本產生的程式庫：

* 根據已安裝的 SDK，以目前的 .NET framework 為目標。
* 藉由包含 MSBuild 專案所支援的平臺，讓瀏覽器相容性檢查平臺相依性 `browser` `SupportedPlatform` 。
* 新增 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)的 NuGet 套件參考。

範例：

```xml
<Project Sdk="Microsoft.NET.Sdk.Razor">

  <PropertyGroup>
    <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Components.Web" Version="{VERSION}" />
  </ItemGroup>

</Project>
```

在上述範例中：

* `{TARGET FRAMEWORK}`預留位置是[TFM)  (的目標 Framework 標記](/dotnet/standard/frameworks)。
* `{VERSION}`預留位置是封裝的版本 [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 。

### <a name="only-support-the-blazor-server-hosting-model"></a>僅支援 Blazor Server 裝載模型

類別庫很少只支援 [Blazor Server](xref:blazor/hosting-models#blazor-server) 應用程式。 如果類別庫需要 [Blazor Server](xref:blazor/hosting-models#blazor-server) 特定的功能，例如或的存取， <xref:Microsoft.AspNetCore.Components.Server.Circuits.CircuitHandler> <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage> 或使用 ASP.NET Core 特定的功能，例如中介軟體、MVC 控制器或 Razor 頁面，請使用下列 **其中一** 種方法：

* 指定當使用 **支援頁面和 views** 核取方塊 (Visual Studio) 或使用命令的選項來建立專案時，程式庫支援頁面和流覽 `-s|--support-pages-and-views` `dotnet new` ：

  ```dotnetcli
  dotnet new razorclasslib -s true
  ```

* 只在程式庫的專案檔中提供架構參考給 ASP.NET Core：

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Razor">

    <ItemGroup>
      <FrameworkReference Include="Microsoft.AspNetCore.App" />
    </ItemGroup>

  </Project>
  ```

### <a name="support-multiple-framework-versions"></a>支援多個架構版本

如果程式庫必須支援 Blazor 在目前版本中新增至的功能，同時也支援一或多個較早的版本，請多目標程式庫。 在 MSBuild 屬性中提供以分號分隔的 [目標 Framework 標記 (tfm) ](/dotnet/standard/frameworks) 清單 `TargetFrameworks` ：

```xml
<Project Sdk="Microsoft.NET.Sdk.Razor">

  <PropertyGroup>
    <TargetFrameworks>{TARGET FRAMEWORKS}</TargetFrameworks>
  </PropertyGroup>

  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Components.Web" Version="{VERSION}" />
  </ItemGroup>

</Project>
```

在上述範例中：

* `{TARGET FRAMEWORKS}`預留位置代表以分號分隔的 tfm 清單。 例如： `netcoreapp3.1;net5.0` 。
* `{VERSION}`預留位置是封裝的版本 [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

從範本產生的專案：

* 目標 .NET Standard 2.0。
* 將 `RazorLangVersion` 屬性設定為 `3.0`。 `3.0` 是 .NET Core 3.x 的預設值。
* 新增下列封裝參考：
  * [AspNetCore 元件](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [AspNetCore Web 元件](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>支援特定的裝載模型

最常見的情況是支援單一 Blazor 裝載模型。 舉例而言， Razor 只支援專案的元件耗用量 [Blazor Server](xref:blazor/hosting-models#blazor-server) ：

* 以 .NET Core 2.x 為目標。
* 新增 `<FrameworkReference>` 共用架構的元素。

例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

::: moniker-end

如需包含元件之程式庫的詳細資訊 Razor ，請參閱 <xref:blazor/components/class-libraries> 。

## <a name="include-mvc-extensibility"></a>包含 MVC 擴充性

本節概述程式庫的建議，包括：

* [Razor views 或 Razor Pages](#razor-views-or-razor-pages)
* [標籤協助程式](#tag-helpers)
* [檢視元件](#view-components)

本節不會討論多目標，以支援多個版本的 MVC。 如需支援多個 ASP.NET Core 版本的指引，請參閱 [支援多個 ASP.NET Core 版本](#support-multiple-aspnet-core-versions)。

### <a name="razor-views-or-razor-pages"></a>Razor views 或 Razor Pages

包含[ Razor 視圖](xref:mvc/views/overview)或[ Razor 頁面](xref:razor-pages/index)的專案必須使用[Microsoft .net Sdk Razor 。SDK](xref:razor-pages/sdk)。

如果專案是以 .NET Core 2.x 為目標，則需要：

* `AddRazorSupportForMvc`設定為的 MSBuild 屬性 `true` 。
* `<FrameworkReference>`共用架構的元素。

**Razor 類別庫** 專案範本滿足上述以 .net Core 2.x 為目標的專案需求。 針對您的編輯器，請使用下列指示。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

使用 [ **Razor 類別庫**] 專案範本。 應選取範本的 [ **支援頁面和流覽** 器] 核取方塊。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在整合式終端機中執行下列命令：

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

目前沒有任何專案範本支援。

---

例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

如果專案是以 .NET Standard 為目標，則需要 [AspNetCore 的 Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) 封裝參考。 `Microsoft.AspNetCore.Mvc`封裝會移至 ASP.NET Core 3.0 的共用架構中，因此不會再發佈。 例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>標籤協助程式

包含 [標記](xref:mvc/views/tag-helpers/intro) 協助程式的專案應該使用 `Microsoft.NET.Sdk` SDK。 如果以 .NET Core 3.x 為目標，請新增 `<FrameworkReference>` 共用架構的元素。 例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

如果目標 .NET Standard (支援早于 ASP.NET Core 3.x) 的版本，請將套件參考新增至[AspNetCore Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)。 `Microsoft.AspNetCore.Mvc.Razor`封裝會移至共用架構中，因此不會再發佈。 例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>檢視元件

包含 [View 元件](xref:mvc/views/view-components) 的專案應該使用 `Microsoft.NET.Sdk` SDK。 如果以 .NET Core 3.x 為目標，請新增 `<FrameworkReference>` 共用架構的元素。 例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

如果目標 .NET Standard (支援早于 ASP.NET Core 3.x) 的版本，請將套件參考新增至 [ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)。 `Microsoft.AspNetCore.Mvc.ViewFeatures`封裝會移至共用架構中，因此不會再發佈。 例如：

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>支援多個 ASP.NET Core 版本

需要多目標才能撰寫支援多種 ASP.NET Core 變體的程式庫。 請考慮標籤協助程式程式庫必須支援下列 ASP.NET Core 變體的案例：

* ASP.NET Core 2.1 的目標 .NET Framework 4.6。1
* 以 .NET Core 2.x 為目標的 ASP.NET Core 2。x
* 以 .NET Core 2.x 為目標的 ASP.NET Core 3。x

下列專案檔透過屬性支援這些變數 `TargetFrameworks` ：

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

使用上述的專案檔：

* `Markdig`針對所有取用者新增套件。
* AspNetCore 的參考[。 Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) 是針對以 .NET Framework 4.6.1 或更新版本或 .NET Core 2.x 為目標的取用者新增的。 因為回溯相容性的原因，套件的版本2.1.0 適用于 ASP.NET Core 2.2。
* 針對以 .NET Core 2.x 為目標的取用者，會參考共用架構。 此 `Microsoft.AspNetCore.Mvc.Razor` 封裝包含在共用架構中。

或者，您可以將 .NET Standard 2.0 的目標設為目標，而不是以 .NET Core 2.1 和 .NET Framework 4.6.1 為目標：

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

使用上述的專案檔時，有下列注意事項：

* 因為此程式庫只包含標籤協助程式，所以以 ASP.NET Core 執行所在的特定平臺為目標更為直接： .NET Core 和 .NET Framework。 標籤協助程式無法供其他 .NET Standard 符合2.0 規範的目標架構（例如 Unity、UWP 和 Xamarin）使用。
* 使用 .NET Framework 中的 .NET Standard 2.0，會有一些在 .NET Framework 4.7.2 中已經解決的問題。 您可以將目標設為 .NET Framework 4.6.1，藉以改善使用 .NET Framework 4.6.1 至4.7.1 的取用者體驗。

如果您的程式庫需要呼叫平臺特定的 Api，請以特定的 .NET 實作為目標，而不是 .NET Standard。 如需詳細資訊，請參閱 [多目標](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)。

## <a name="use-an-api-that-hasnt-changed"></a>使用未變更的 API

想像您要將中介軟體程式庫從 .NET Core 2.2 升級到3.0 的案例。 程式庫中使用的 ASP.NET Core 中介軟體 Api 未在 ASP.NET Core 2.2 和3.0 之間變更。 若要繼續支援 .NET Core 3.0 中的中介軟體程式庫，請執行下列步驟：

* 遵循 [標準程式庫指引](/dotnet/standard/library-guidance/)。
* 如果對應的元件不存在於共用架構中，請為每個 API 的 NuGet 套件新增套件參考。

## <a name="use-an-api-that-changed"></a>使用變更的 API

想像您要將程式庫從 .NET Core 2.2 升級至 .NET Core 3.0 的案例。 程式庫中使用的 ASP.NET Core API 在 ASP.NET Core 3.0 中有 [重大變更](/dotnet/core/compatibility/breaking-changes) 。 請考慮是否可以重寫程式庫，而不在所有版本中使用中斷的 API。

如果您可以重寫程式庫，請執行此動作，並繼續以較早的目標 framework 為目標 (例如 .NET Standard 2.0 或使用套件參考 .NET Framework 4.6.1) 。

如果您無法重寫程式庫，請執行下列步驟：

* 新增 .NET Core 3.0 的目標。
* 新增 `<FrameworkReference>` 共用架構的元素。
* 使用 [#if 預處理器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) 指示詞搭配適當的目標 framework 符號，有條件地編譯器代碼。

例如，HTTP 要求和回應資料流程的同步讀取和寫入預設會在 ASP.NET Core 3.0 時停用。 ASP.NET Core 2.2 預設支援同步行為。 假設有一個中介軟體程式庫，應該在發生 i/o 的位置啟用同步讀取和寫入。 程式庫應該在適當的預處理器指示詞中包含程式碼，以啟用同步功能。 例如：

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>使用3.0 中引進的 API

假設您想要使用 ASP.NET Core 3.0 中引進的 ASP.NET Core API。 請考量下列問題：

1. 程式庫功能是否需要新的 API？
1. 程式庫可以用不同的方式來執行這項功能嗎？

如果媒體櫃的功能需要 API，而且沒有任何方法可將它向下層級執行：

* 僅限以 .NET Core 2.x 為目標。
* 新增 `<FrameworkReference>` 共用架構的元素。

如果程式庫可以使用不同的方式來執行此功能：

* 新增 .NET Core 3.x 作為目標 framework。
* 新增 `<FrameworkReference>` 共用架構的元素。
* 使用 [#if 預處理器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) 指示詞搭配適當的目標 framework 符號，有條件地編譯器代碼。

例如，下列標記協助程式會使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> ASP.NET Core 3.0 中引進的介面。 以 .NET Core 3.0 為目標的取用者會執行目標 framework 符號所定義的程式碼路徑 `NETCOREAPP3_0` 。 標籤協助程式的函式參數類型會 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 針對 .Net Core 2.1 和 .NET Framework 4.6.1 取用者變更為。 這是必要的變更，因為 ASP.NET Core 3.0 標示 `IHostingEnvironment` 為已淘汰，建議 `IWebHostEnvironment` 取代。

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

下列多目標專案檔支援此標籤協助程式案例：

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>使用從共用架構移除的 API

若要使用從共用架構中移除的 ASP.NET Core 元件，請新增適當的封裝參考。 如需 ASP.NET Core 3.0 中從共用架構移除的封裝清單，請參閱 [移除淘汰的封裝參考](xref:migration/22-to-30#remove-obsolete-package-references)。

例如，若要新增 web API 用戶端：

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>其他資源

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [.NET 實作支援](/dotnet/standard/net-standard#net-implementation-support)
* [.NET 支援原則](https://dotnet.microsoft.com/platform/support/policy)
