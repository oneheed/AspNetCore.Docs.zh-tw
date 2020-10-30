---
title: Razor ASP.NET Core 中的檔案編譯
author: rick-anderson
description: 瞭解如何 Razor 在 ASP.NET Core 應用程式中進行檔案編譯。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059074"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor ASP.NET Core 中的檔案編譯

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razor具有 *cshtml* 副檔名的檔案會使用 [ Razor SDK](xref:razor-pages/sdk)在組建和發行時間進行編譯。 您可以藉由設定專案，選擇性地啟用執行時間編譯。

## <a name="no-locrazor-compilation"></a>Razor 編譯

RazorSDK 預設會啟用檔案的組建階段和發行時間編譯 Razor 。 啟用時，執行時間編譯會補充組建時間編譯，讓檔案在編輯時可 Razor 進行更新。

## <a name="enable-runtime-compilation-at-project-creation"></a>在專案建立時啟用執行時間編譯

Razor頁面和 MVC 專案範本包含一個選項，可讓您在建立專案時啟用執行時間編譯。 ASP.NET Core 3.1 和更新版本中支援此選項。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中：

1. 選取 **Web 應用程式** 或 **web 應用程式 (模型-視圖控制器)** 專案範本。
1. 選取 [ **啟用 Razor 執行時間編譯** ] 核取方塊。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

使用 `-rrc` 或 `--razor-runtime-compilation` 範本選項。 例如，下列命令會建立 Razor 啟用執行時間編譯的新頁面專案：

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>在現有的專案中啟用執行時間編譯

若要針對現有專案中的所有環境啟用執行時間編譯：

1. 請安裝 [AspNetCore Razor 。>microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 套件。
1. 更新專案的 `Startup.ConfigureServices` 方法以包含的呼叫 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 。 例如：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>有條件地在現有的專案中啟用執行時間編譯

您可以啟用執行時間編譯，使其僅適用于本機開發。 有條件地以這種方式啟用，可確保已發佈的輸出：

* 使用編譯的視圖。
* 不會在生產環境中啟用檔案監看員。

若只要啟用開發環境中的執行時間編譯：

1. 請安裝 [AspNetCore Razor 。>microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 套件。
1. 修改launchSettings.js中的啟動設定檔 `environmentVariables` 區段： *launchSettings.json*
    * 確認 `ASPNETCORE_ENVIRONMENT` 設定為 `"Development"` 。
    * 請設定 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 為 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`。

在下列範例中，會在 `IIS Express` 和啟動設定檔的開發環境中啟用執行時間編譯 `RazorPagesApp` ：

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

專案類別中不需要變更程式碼 `Startup` 。 在執行時間，ASP.NET Core 搜尋中的 [元件層級 HostingStartup 屬性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 。 `HostingStartup`屬性會指定要執行的應用程式啟動程式碼。 該啟始程式碼會啟用執行時間編譯。

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>啟用類別庫的執行時間編譯 Razor

假設有一個案例，其中的 Razor 頁面專案參考了 [ Razor 類別庫 (](xref:razor-pages/ui-class)名為 *MyClassLib* 的 RCL) 。 RCL 包含您所有小組的 MVC 和頁面專案都使用的 *_Layout cshtml* 檔案 Razor 。 您想要針對該 RCL 中的 *_Layout cshtml* 檔案啟用執行時間編譯。 在頁面專案中進行下列變更 Razor ：

1. 以有條件的指示啟用執行時間編譯，在 [現有的專案中啟用執行時間編譯](#conditionally-enable-runtime-compilation-in-an-existing-project)。
1. 設定中的執行時間編譯選項 `Startup.ConfigureServices` ：

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    在上述程式碼中，會對 *MyClassLib* RCL 的絕對路徑進行結構化。 [PHYSICALFILEPROVIDER API](xref:fundamentals/file-providers#physicalfileprovider)是用來尋找位於該絕對路徑的目錄和檔案。 最後， `PhysicalFileProvider` 會將實例新增至檔案提供者集合，以允許存取 RCL 的 *cshtml* 檔案。

## <a name="additional-resources"></a>其他資源

* [ Razor CompileOnBuild 和 Razor CompileOnPublish](xref:razor-pages/sdk#properties)屬性。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor具有 *cshtml* 副檔名的檔案會使用 [ Razor SDK](xref:razor-pages/sdk)在組建和發行時間進行編譯。 您可以透過設定應用程式，選擇性地啟用執行階段編譯。

## <a name="no-locrazor-compilation"></a>Razor 編譯

RazorSDK 預設會啟用檔案的組建階段和發行時間編譯 Razor 。 啟用時，執行時間編譯會補充組建時間編譯，讓檔案在編輯時可 Razor 進行更新。

## <a name="runtime-compilation"></a>執行階段編譯

若要啟用所有環境和設定模式的執行時間編譯：

1. 請安裝 [AspNetCore Razor 。>microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 套件。

1. 更新專案的 `Startup.ConfigureServices` 方法以包含的呼叫 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 。 例如：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>有條件地啟用執行時間編譯

您可以啟用執行時間編譯，使其僅適用于本機開發。 有條件地以這種方式啟用，可確保已發佈的輸出：

* 使用編譯的視圖。
* 大小較小。
* 不會在生產環境中啟用檔案監看員。

若要根據環境和設定模式啟用執行時間編譯：

1. 有條件地參考 [AspNetCore Razor 。](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 以作用中的值為基礎的 >microsoft.aspnetcore.mvc.razor.runtimecompilation 套件 `Configuration` ：

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. 更新專案的 `Startup.ConfigureServices` 方法以包含的呼叫 `AddRazorRuntimeCompilation` 。 有條件 `AddRazorRuntimeCompilation` 地執行，如此 `ASPNETCORE_ENVIRONMENT` 一來，當變數設定為時，它才會在 Debug 模式中執行 `Development` ：

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>其他資源

* [ Razor CompileOnBuild 和 Razor CompileOnPublish](xref:razor-pages/sdk#properties)屬性。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* 請參閱 [GitHub 上的執行時間編譯範例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) ，以取得可讓執行時間編譯跨專案運作的範例。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

當叫用 Razor 相關聯的 Razor 頁面或 MVC 視圖時，會在執行時間編譯檔案。 Razor檔案會使用[ Razor SDK](xref:razor-pages/sdk)在組建和發行時間進行編譯。

## <a name="no-locrazor-compilation"></a>Razor 編譯

RazorSDK 預設會啟用檔案的組建和發行時間編譯 Razor 。 Razor在檔案更新後編輯檔案，會在組建階段受到支援。 依預設，只有編譯的 *Views.dll* 以及編譯檔案時所需的任何 *cshtml* 檔案或參考元件 Razor 會隨您的應用程式一起部署。

> [!IMPORTANT]
> 先行編譯工具已被取代，且將在 ASP.NET Core 3.0 中移除。 建議您遷移至[ Razor Sdk](xref:razor-pages/sdk)。
>
> Razor只有在專案檔中未設定任何先行編譯特定屬性時，SDK 才會生效。 例如，將 *.csproj* 檔案的 `MvcRazorCompileOnPublish` 屬性設定為停用 `true` Razor SDK。

## <a name="runtime-compilation"></a>執行階段編譯

組建階段編譯是由檔案的執行時間編譯所補充 Razor 。 Razor當 *cshtml* 檔案的內容變更時，ASP.NET Core MVC 會重新編譯檔案。

## <a name="additional-resources"></a>其他資源

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
