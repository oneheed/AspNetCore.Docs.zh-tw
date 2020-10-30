---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 瞭解 Razor ASP.NET Core 中的頁面如何讓撰寫以頁面為焦點的案例撰寫程式碼比使用 MVC 更簡單、更具生產力。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060868"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>ASP.NET Core Razor SDK

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>概觀

[!INCLUDE[](~/includes/2.1-SDK.md)]包含 `Microsoft.NET.Sdk.Razor` MSBuild sdk (Razor SDK) 。 RazorSDK：

::: moniker range=">= aspnetcore-3.0"

* 需要建立、封裝和發佈包含 [Razor](xref:mvc/views/razor) ASP.NET CORE MVC 或專案之檔案的專案 [Blazor](xref:blazor/index) 。
* 包含一組預先定義的目標、屬性和專案，可讓您自訂 Razor (的 *cshtml* 或 *razor* ) 檔的編譯。

RazorSDK 包含 `Content` `Include` 屬性設定為和萬用字元模式的 `**\*.cshtml` 專案 `**\*.razor` 。 已發行相符的檔案。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 將建立、封裝和發行專案的體驗標準化，包含 [Razor](xref:mvc/views/razor) ASP.NET CORE MVC 專案的檔案。
* 包含一組預先定義的目標、屬性和專案，可讓您自訂檔案的編譯 Razor 。

RazorSDK 包含 `Content` `Include` 屬性設定為 `**\*.cshtml` 萬用字元模式的專案。 已發行相符的檔案。

::: moniker-end

## <a name="prerequisites"></a>必要條件

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>使用 Razor SDK

大部分的 web 應用程式都不需要明確參考 Razor SDK。

::: moniker range=">= aspnetcore-3.0"

若要使用 Razor SDK 來建立包含 Razor 視圖或頁面的類別庫 Razor ，建議您從 Razor 類別庫 (RCL) 專案範本開始。 用來建立 Blazor ( *razor* ) 檔案的 RCL，至少需要 [AspNetCore 元件](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) 封裝的參考。 用來 Razor ( *cshtml* 檔案建立 views 或頁面的 RCL，) 至少需要目標 `netcoreapp3.0` 或更新版本，並且 `FrameworkReference` 在其專案檔中 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) 。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

若要使用 Razor SDK 來建立包含 Razor 視圖或頁面的類別庫 Razor ：

* 使用 `Microsoft.NET.Sdk.Razor` 來取代 `Microsoft.NET.Sdk`：

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 一般而言，需要的封裝參考 `Microsoft.AspNetCore.Mvc` 才能接收建立和編譯頁面和瀏覽器所需的其他相依性 Razor Razor 。 您的專案至少應將套件參考新增至：

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design`封裝會提供 Razor 專案的編譯工作和目標。

  `Microsoft.AspNetCore.Mvc` 中包含上述套件。 下列標記顯示的專案檔會使用 Razor SDK 來建立 Razor ASP.NET Core Razor Pages 應用程式的檔案：

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`和 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 套件都包含在[AspNetCore. App 中繼套件](xref:fundamentals/metapackage-app)中。 不過，無版本 `Microsoft.AspNetCore.App` 套件參考會提供應用程式的中繼套件，而不包含最新的版本 `Microsoft.AspNetCore.Razor.Design` 。 專案必須參考一致版本的 `Microsoft.AspNetCore.Razor.Design` (或 `Microsoft.AspNetCore.Mvc`) ，才能包含的最新組建階段修正 Razor 。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/aspnet/Razor/issues/2553)。

::: moniker-end

### <a name="properties"></a>屬性

下列屬性會控制 Razor 作為專案組建一部分的 SDK 行為：

* `RazorCompileOnBuild`：在 `true` 建立專案時，編譯和發出元件的時間 Razor 。 預設值為 `true`。
* `RazorCompileOnPublish`：在 `true` 發行專案時，編譯和發出元件的時間 Razor 。 預設值為 `true`。

下表中的屬性和專案可用來設定 SDK 的輸入和輸出 Razor 。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 從 ASP.NET Core 3.0 開始， Razor 如果 `RazorCompileOnBuild` 專案檔中的或 MSBuild 屬性已停用，則預設不會提供 MVC 視圖或頁面 `RazorCompileOnPublish` 。 應用程式必須加入 AspNetCore 的明確參考 [ Razor 。](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) 如果應用程式依賴執行時間編譯來處理 *cshtml* 檔案，則為 >microsoft.aspnetcore.mvc.razor.runtimecompilation 套件。

::: moniker-end

| 項目 | 描述 |
| ----- | ----------- |
| `RazorGenerate` | 專案專案 (做為產生程式碼之輸入的 cshtml 檔案) *。* |
| `RazorComponent` | 專案專案 ( *razor* 檔案) ，其為元件程式 Razor 代碼產生的輸入。 |
| `RazorCompile` | 專案元素 ( *.cs* 檔案) ，其為編譯目標的輸入。 Razor 您可以使用這個 `ItemGroup` 來指定要編譯到元件中的其他檔案 Razor 。 |
| `RazorTargetAssemblyAttribute` | 用來撰寫程式碼的專案專案會產生元件的屬性 Razor 。 例如：  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 將專案專案新增為所產生元件中的內嵌資源 Razor 。 |

::: moniker range=">= aspnetcore-3.0"

| 屬性 | 描述 |
| -------- | ----------- |
| `RazorTargetName` | 檔案名 (沒有所產生之元件的副檔名) Razor 。 |
| `RazorOutputPath` | Razor輸出目錄。 |
| `RazorCompileToolset` | 用來判斷用來建立元件的工具組 Razor 。 有效值是 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 預設值為 `true`。 當 `true` 為時，會包含 *web.config* 、 *. json* 和 *cshtml* 檔案做為專案中的內容。 透過參考時 `Microsoft.NET.Sdk.Web` ，也會包含 *wwwroot* 和設定檔下的檔案。 |
| `EnableDefaultRazorGenerateItems` | 如果是 `true`，請包括來自 `RazorGenerate` 項目之 `Content` 項目的 *.cshtml* 檔案。 |
| `GenerateRazorTargetAssemblyInfo` | 當 `true` 為時，會產生包含所指定之屬性的 *.cs* 檔案 `RazorAssemblyAttribute` ，而且會在編譯輸出中包含檔案。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | 如果是 `true`，請將一組預設的組件屬性新增至 `RazorAssemblyAttribute`。 |
| `CopyRazorGenerateFilesToPublishDirectory` | 當 `true` 為時，會將 `RazorGenerate` 專案 ( *. cshtml* ) 檔案複製到發行目錄。 Razor如果已發行的應用程式在組建階段或發行時間參與編譯，通常不需要這些檔案。 預設值為 `false`。 |
| `PreserveCompilationReferences` | 如果是 `true`，請將參考組件項目複製到發行目錄。 一般來說，如果 Razor 在組建階段或發行時間進行編譯，則發行的應用程式不需要參考元件。 `true`如果您已發佈的應用程式需要執行時間編譯，請將設定為。 例如， `true` 如果應用程式在執行時間修改 *cshtml* 檔案，或使用內嵌的視圖，請將值設定為。 預設值為 `false`。 |
| `IncludeRazorContentInPack` | 若 `true` 為，則 Razor 會在產生的 NuGet 套件中標示要包含的所有內容專案 (的) *。* 預設值為 `false`。 |
| `EmbedRazorGenerateSources` | 當 `true` 為時，會將 Razor 產生 ( *. cshtml* ) 專案做為內嵌檔案新增至產生的 Razor 元件。 預設值為 `false`。 |
| `UseRazorBuildServer` | 如果是 `true`，請使用持續性組建伺服器處理序來卸載程式碼產生工作。 預設值為 `UseSharedCompilation`。 |
| `GenerateMvcApplicationPartsAssemblyAttributes` | 當 `true` 為時，SDK 會在執行時間產生 MVC 用來執行應用程式元件探索的其他屬性。 |
| `DefaultWebContentItemExcludes` | 專案專案的萬用字元模式，這些專案會 `Content` 在以 Web 或 SDK 為目標的專案中，從專案群組中排除 Razor |
| `ExcludeConfigFilesFromBuildOutput` | When `true` 、 *.config* 和 *. json* 檔案不會複製到組建輸出目錄中。 |
| `AddRazorSupportForMvc` | 當為時 `true` ，設定 Razor SDK 以新增在建立包含 mvc 視圖或頁面的應用程式時所需的 MVC 設定支援 Razor 。 針對以 Web SDK 為目標的 .NET Core 3.0 或更新版本專案，會隱含設定這個屬性。 |
| `RazorLangVersion` | Razor要設為目標的語言版本。 |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| 屬性 | 描述 |
| -------- | ----------- |
| `RazorTargetName` | 檔案名 (沒有所產生之元件的副檔名) Razor 。 |
| `RazorOutputPath` | Razor輸出目錄。 |
| `RazorCompileToolset` | 用來判斷用來建立元件的工具組 Razor 。 有效值是 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 預設值為 `true`。 當 `true` 為時，會包含 *web.config* 、 *. json* 和 *cshtml* 檔案做為專案中的內容。 透過參考時 `Microsoft.NET.Sdk.Web` ，也會包含 *wwwroot* 和設定檔下的檔案。 |
| `EnableDefaultRazorGenerateItems` | 如果是 `true`，請包括來自 `RazorGenerate` 項目之 `Content` 項目的 *.cshtml* 檔案。 |
| `GenerateRazorTargetAssemblyInfo` | 當 `true` 為時，會產生包含所指定之屬性的 *.cs* 檔案 `RazorAssemblyAttribute` ，而且會在編譯輸出中包含檔案。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | 如果是 `true`，請將一組預設的組件屬性新增至 `RazorAssemblyAttribute`。 |
| `CopyRazorGenerateFilesToPublishDirectory` | 當 `true` 為時，會將 `RazorGenerate` 專案 ( *. cshtml* ) 檔案複製到發行目錄。 Razor如果已發行的應用程式在組建階段或發行時間參與編譯，通常不需要這些檔案。 預設值為 `false`。 |
| `CopyRefAssembliesToPublishDirectory` | 如果是 `true`，請將參考組件項目複製到發行目錄。 一般來說，如果 Razor 在組建階段或發行時間進行編譯，則發行的應用程式不需要參考元件。 `true`如果您已發佈的應用程式需要執行時間編譯，請將設定為。 例如， `true` 如果應用程式在執行時間修改 *cshtml* 檔案，或使用內嵌的視圖，請將值設定為。 預設值為 `false`。 |
| `IncludeRazorContentInPack` | 若 `true` 為，則 Razor 會在產生的 NuGet 套件中標示要包含的所有內容專案 (的) *。* 預設值為 `false`。 |
| `EmbedRazorGenerateSources` | 當 `true` 為時，會將 Razor 產生 ( *. cshtml* ) 專案做為內嵌檔案新增至產生的 Razor 元件。 預設值為 `false`。 |
| `UseRazorBuildServer` | 如果是 `true`，請使用持續性組建伺服器處理序來卸載程式碼產生工作。 預設值為 `UseSharedCompilation`。 |
| `GenerateMvcApplicationPartsAssemblyAttributes` | 當 `true` 為時，SDK 會在執行時間產生 MVC 用來執行應用程式元件探索的其他屬性。 |
| `DefaultWebContentItemExcludes` | 專案專案的萬用字元模式，這些專案會 `Content` 在以 Web 或 SDK 為目標的專案中，從專案群組中排除 Razor |
| `ExcludeConfigFilesFromBuildOutput` | When `true` 、 *.config* 和 *. json* 檔案不會複製到組建輸出目錄中。 |
| `AddRazorSupportForMvc` | 當為時 `true` ，設定 Razor SDK 以新增在建立包含 mvc 視圖或頁面的應用程式時所需的 MVC 設定支援 Razor 。 針對以 Web SDK 為目標的 .NET Core 3.0 或更新版本專案，會隱含設定這個屬性。 |
| `RazorLangVersion` | Razor要設為目標的語言版本。 |

::: moniker-end

如需屬性的詳細資訊，請參閱 [MSBuild 屬性](/visualstudio/msbuild/msbuild-properties)。

### <a name="targets"></a>目標

RazorSDK 會定義兩個主要目標：

* `RazorGenerate`：程式碼會從專案元素產生 *.cs* 檔案。 `RazorGenerate` 您 `RazorGenerateDependsOn` 可以使用屬性來指定可在此目標之前或之後執行的其他目標。
* `RazorCompile`：將產生的 *.cs* 檔案編譯成 Razor 元件。 使用 `RazorCompileDependsOn` 指定可在此目標之前或之後執行的其他目標。
* `RazorComponentGenerate`：程式碼會產生專案元素的 *.cs 檔案。* `RazorComponent` 您 `RazorComponentGenerateDependsOn` 可以使用屬性來指定可在此目標之前或之後執行的其他目標。

### <a name="runtime-compilation-of-no-locrazor-views"></a>執行時間編譯 Razor 視圖

* 根據預設， Razor SDK 不會發行執行執行時間編譯所需的參考元件。 當應用程式模型依賴執行階段編譯時，這會導致編譯失敗&mdash;例如，發佈應用程式之後，應用程式使用內嵌的檢視或變更檢視。 將 `CopyRefAssembliesToPublishDirectory` 設為 `true` 以繼續發佈參考組件。

* 針對 web 應用程式，請確定您的應用程式是以 SDK 為目標 `Microsoft.NET.Sdk.Web` 。

## <a name="no-locrazor-language-version"></a>Razor 語言版本

以 SDK 為目標時 `Microsoft.NET.Sdk.Web` ， Razor 會從應用程式的目標 framework 版本推斷語言版本。 針對以 SDK 為目標的專案 `Microsoft.NET.Sdk.Razor` ，或在罕見的情況下，應用程式所需的 Razor 語言版本與推斷值不同，您可以藉由在 `<RazorLangVersion>` 應用程式的專案檔中設定屬性來設定版本：

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor的語言版本與所建立的執行階段版本緊密整合。 不支援以不是針對執行時間設計的語言版本為目標，而且可能會產生組建錯誤。

## <a name="additional-resources"></a>其他資源

* [適用於 .NET Core 之 csproj 格式的新增項目](/dotnet/core/tools/csproj)
* [一般 MSBuild 專案項目](/visualstudio/msbuild/common-msbuild-project-items)
