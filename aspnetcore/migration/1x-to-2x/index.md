---
title: 從 ASP.NET Core 1.x 遷移至 2.0
author: rick-anderson
description: 本文概述將 ASP.NET Core 1.x 專案移轉至 ASP.NET Core 2.0 的必要條件和最常見步驟。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: 9a175c8a3cd960404006aad559b2ca65b5a8c1e1
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564220"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>從 ASP.NET Core 1.x 遷移至 2.0

作者：[Scott Addie](https://github.com/scottaddie)

在本文中，我們會逐步引導您將現有的 ASP.NET Core 1.x 專案更新為 ASP.NET Core 2.0。 將應用程式移轉至 ASP.NET Core 2.0 可讓您充分利用[許多新功能和效能改進](xref:aspnetcore-2.0)。

現有的 ASP.NET Core 1.x 應用程式是根據特定版本的專案範本。 隨著 ASP.NET Core 架構發展，其中內含的專案範本和起始程式碼也跟著演進。 除了更新 ASP.NET Core 架構之外，您還需要更新應用程式的程式碼。

<a name="prerequisites"></a>

## <a name="prerequisites"></a>必要條件

請參閱 [ASP.NET Core 使用者入門](xref:getting-started)。

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>更新 Target Framework Moniker (TFM)

以 .NET Core 為目標的專案應該使用版本大於或等於 .NET Core 2.0 的 [TFM](/dotnet/standard/frameworks)。 在 *.csproj* 檔案中搜尋 `<TargetFramework>` 節點，並以 `netcoreapp2.0` 取代其內部文字：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

以 .NET Framework 為目標的專案應該使用版本大於或等於 .NET Framework 4.6.1 的 TFM。 在 *.csproj* 檔案中搜尋 `<TargetFramework>` 節點，並以 `net461` 取代其內部文字：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2.0 提供了比 .NET Core 1.x 更大的介面區。 如果您只是因為 .NET Core 1.x 中遺漏了 API 而以 .NET Framework 為目標，以 .NET Core 2.0 為目標可能有效。

如果專案檔包含 `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`，請參閱[這個 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268) \(英文\)。

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>更新 global.json 中的 .NET Core SDK 版本

如果您的方案依賴 [global.json](/dotnet/core/tools/global-json) 檔案而以特定的 .NET Core SDK 版本為目標，請更新其 `version` 屬性，以使用您電腦上安裝的 2.0 版：

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>更新套件參考

1.x 專案中的 *.csproj* 檔案列出專案所使用的每個 NuGet 套件。

在以 .NET Core 2.0 為目標的 ASP.NET Core 2.0 專案中，*.csproj* 檔案中的單一 [中繼套件](xref:fundamentals/metapackage) metapackage 參考會取代套件的集合：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

中繼套件包含 ASP.NET Core 2.0 和 Entity Framework Core 2.0 的所有功能。

以 .NET Framework 為目標時，ASP.NET Core 2.0 專案應該繼續參考個別的 NuGet 套件。 將每個 `<PackageReference />` 節點的 `Version` 屬性更新為 2.0.0。

例如，以下是以 .NET Framework 為目標的典型 ASP.NET Core 2.0 專案所使用的 `<PackageReference />` 節點清單：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>更新 .NET Core CLI 工具

在 *.csproj* 檔案中，將每個 `<DotNetCliToolReference />` 節點的 `Version` 屬性更新為 2.0.0。

例如，以下是以 .NET Core 2.0 為目標的典型 ASP.NET Core 2.0 專案所使用的 CLI 工具清單：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>重新命名套件目標後援屬性

1.x 專案的 *.csproj* 檔案使用 `PackageTargetFallback` 節點和變數：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

將節點與變數都重新命名為 `AssetTargetFallback`：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>更新 Program.cs 中的 Main 方法

在 1.x 專案中，*Program.cs* 的 `Main` 方法看起來像這樣：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

在 2.0 專案中，*Program.cs* 的 `Main` 方法已經過簡化：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

強烈建議您採用這個新的 2.0 模式，像[Entity Framework (EF) Core 移轉](xref:data/ef-mvc/migrations)這類的產品功能必須有這個模式才能運作。 例如，從 [套件管理員主控台] 視窗執行 `Update-Database` 或從命令列 (在轉換成 ASP.NET Core 2.0 的專案中) 執行 `dotnet ef database update` 會產生下列錯誤：

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>新增組態提供者

在 1.x 專案中，若要將組態提供者新增至應用程式，必須透過 `Startup` 建構函式。 其中涉及的步驟包括建立 `ConfigurationBuilder` 的執行個體、載入適用的提供者 (環境變數、應用程式設定等等)，以及初始化 `IConfigurationRoot` 的成員。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

上述範例會載入 `Configuration` 具有設定的成員，以及 *appsettings.json* 任何 *appsettings。 \<EnvironmentName\>* 符合屬性的 json 檔案 `IHostingEnvironment.EnvironmentName` 。 這些檔案的位置與 *Startup.cs* 的路徑相同。

在 2.0 專案中，1.x 專案固有的模板組態程式碼會在幕後執行。 例如，環境變數及應用程式設定會在啟動時載入。 對等的 *Startup.cs* 程式碼會縮減成為插入執行個體的 `IConfiguration` 初始化：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

若要移除 `WebHostBuilder.CreateDefaultBuilder` 所新增的預設提供者，請叫用 `ConfigureAppConfiguration` 內 `IConfigurationBuilder.Sources` 屬性上的 `Clear` 方法。 若要將提供者新增回來，請利用 *Program.cs* 中的 `ConfigureAppConfiguration` 方法：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

[此處](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152) \(英文\) 有前述程式碼片段中 `CreateDefaultBuilder` 方法所使用的組態。

如需詳細資訊，請參閱 [ASP.NET Core 中的組態](xref:fundamentals/configuration/index)。

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>移動資料庫初始化程式碼

在使用 EF Core 1.x 的 1.x 專案中，`dotnet ef migrations add` 這類命令會執行下列作業：

1. 將 `Startup` 執行個體具現化
1. 叫用 `ConfigureServices` 方法以註冊所有具相依性導入的服務 (包括 `DbContext` 類型)
1. 執行其必要工作

在使用 EF Core 2.0 的 2.0 專案中，則會叫用 `Program.BuildWebHost` 來取得應用程式服務。 與 1.x 不同的是，這會有叫用 `Startup.Configure` 的額外副作用。 如果您的 1.x 應用程式在其 `Configure` 方法中叫用了資料庫初始化程式碼，可能會發生未預期的問題。 例如，如果資料庫尚不存在，植入程式碼會在 EF Core 移轉命令執行前就執行程式碼。 如果資料庫尚不存在，這個問題會造成 `dotnet ef migrations list` 命令失敗。

請考慮在 *Startup.cs* 的 `Configure` 方法中使用下列 1.x 植入初始化程式碼：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

在 2.0 專案中，將 `SeedData.Initialize` 呼叫移到 *Program.cs* 的 `Main` 方法：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

從 2.0 開始，除了建置及設定網頁主機外，並不適合在 `BuildWebHost` 中執行任何作業。 有關執行應用程式的任何作業都應該在  外處理 `BuildWebHost` &mdash; 通常會在 *Program.cs* 的 `Main` 方法中處理。

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>審核 Razor 視圖編譯設定

更快速的應用程式啟動時間和較小的發行組合對您而言極為重要。 基於這些理由，ASP.NET Core 2.0 中預設會啟用[ Razor view 編譯](xref:mvc/views/view-compilation)。

已不再需要將 `MvcRazorCompileOnPublish` 屬性設定為 true。 除非您停用檢視編譯，否則屬性可能會從 *.csproj* 檔案中移除。

以 .NET Framework 為目標時，您仍然需要明確參考 [AspNetCore Razor 。](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) 在 *.csproj* 檔案中 >microsoft.aspnetcore.mvc.razor.viewcompilation NuGet 套件：

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>依賴 Application Insights「啟動」功能

輕鬆安裝應用程式效能檢測很重要。 您現在可以依賴 Visual Studio 2017 工具中提供的 [Application Insights](/azure/application-insights/app-insights-overview)「啟動」功能。

根據預設，在 Visual Studio 2017 中建立的 ASP.NET Core 1.1 專案已新增 Application Insights。 如果您不要直接使用 Application Insights SDK，請在 *Program.cs* 和 *Startup.cs* 之外，遵循下列步驟：

1. 如果以 .NET Core 為目標，請從 *.csproj* 檔案中移除下列 `<PackageReference />` 節點：

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. 如果以 .NET Core 為目標，請從 *Program.cs* 中移除 `UseApplicationInsights` 擴充方法引動過程：

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. 從 *_Layout.cshtml* 中移除 Application Insights 用戶端 API 呼叫。 它包含下列兩行程式碼：

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

如果您要直接使用 Application Insights SDK，請繼續執行這項操作。 2.0 [中繼套件](xref:fundamentals/metapackage)含有最新版本的 Application Insights，因此如果您參考的是較舊的版本，就會出現套件降級錯誤。

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>採用驗證/ Identity 改進

ASP.NET Core 2.0 有新的驗證模型和的許多重大變更 ASP.NET Core Identity 。 如果您已在啟用個別使用者帳戶的情況下建立您的專案，或您已手動新增驗證或 Identity ，請參閱 [遷移驗證以及 Identity ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)。

## <a name="additional-resources"></a>其他資源

* [ASP.NET Core 2.0 的重大變更](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
