---
title: Razor具有 ASP.NET Core 的類別庫中可重複使用的 UI
author: Rick-Anderson
description: 說明如何 Razor 在 ASP.NET Core 的類別庫中，使用部分視圖建立可重複使用的 UI。
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571013"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>使用 Razor ASP.NET Core 中的類別庫專案建立可重複使用的 UI

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor您可以將視圖、頁面、控制器、頁面模型、 [ Razor 元件](xref:blazor/components/class-libraries)、[視圖元件](xref:mvc/views/view-components)和資料模型內建至 Razor 類別庫 (RCL) 。 RCL 可以封裝和重複使用。 應用程式可以包括 RCL，以及覆寫它所包含的檢視和頁面。 當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>建立包含 UI 的類別庫 Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 Visual Studio 選取 [ **建立新專案**]。
* 選取 [ **Razor 類別庫** > **下一步]**。
* 將程式庫命名 (例如，" Razor ClassLib" ) > **建立**]。 若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。
* 如果您需要支援 views，請選取 [ **支援頁面和視圖** ]。 依預設，只 Razor 支援頁面。 選取 [建立]。

Razor依預設，類別庫 (RCL) 範本預設為 Razor 元件開發。 **支援頁面和 views** 選項支援頁面和視圖。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

從命令列執行 `dotnet new razorclasslib`。 例如：

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Razor依預設，類別庫 (RCL) 範本預設為 Razor 元件開發。 將 `--support-pages-and-views` 選項傳遞 (`dotnet new razorclasslib --support-pages-and-views`) ，以提供頁面和視圖的支援。

如需詳細資訊，請參閱 [dotnet new](/dotnet/core/tools/dotnet-new)。 若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。

---

將檔案新增 Razor 至 RCL。

ASP.NET Core 範本會假設 RCL 內容位於 [ *區域* ] 資料夾中。 請參閱 [RCL 頁面版面](#rcl-pages-layout) 配置來建立可公開內容的 RCL， `~/Pages` 而不是 `~/Areas/Pages` 。

## <a name="reference-rcl-content"></a>參考 RCL 內容

RCL 可以由下列各項參考：

* NuGet 套件。 請參閱[建立 NuGet 套件](/nuget/create-packages/creating-a-package)和 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 和[建立和發佈 NuGet 套件](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。
* *{ProjectName}.csproj*。 請參閱 [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)。

## <a name="override-views-partial-views-and-pages"></a>覆寫檢視、部分檢視和頁面

當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。 例如，將 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml* 新增至 WebApp1，WebApp1 中的 page1 將優先于 RCL 中的 page1。

在下載範例中，將 *WebApp1/Areas/MyFeature2* 重新命名為 *WebApp1/Areas/MyFeature* 以測試優先順序。

將 *Razor UIClassLib/Areas/MyFeature/Pages/shared/_Message. cshtml* partial View 複製到 *WebApp1/Areas/MyFeature/pages/shared/_Message cshtml*。 更新標記來指示新的位置。 建置並執行應用程式，以確認正在使用部分檢視的應用程式版本。

### <a name="rcl-pages-layout"></a>RCL 頁面版面配置

若要參考 RCL 內容（如同 web 應用程式的 *Pages* 資料夾的一部分），請使用下列檔案結構來建立 RCL 專案：

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

假設 *Razor UIClassLib/Pages/Shared* 包含兩個部分檔案： *_Header cshtml* 和 *_Footer. cshtml*。 `<partial>`標記可新增至 _Layout 的 *cshtml* 檔案：

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

將 *_ViewStart cshtml* 檔案新增至 RCL 專案的 *Pages* 資料夾，以使用來自主機 web 應用程式的 *_Layout cshtml* 檔案：

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a>建立具有靜態資產的 RCL

RCL 可能需要隨附的靜態資產，可由 RCL 或 RCL 的取用應用程式參考。 ASP.NET Core 可讓您建立 RCLs，其中包含可供取用應用程式使用的靜態資產。

若要在 RCL 中包含隨附的資產，請在類別庫中建立 *wwwroot* 資料夾，並在該資料夾中包含任何必要的檔案。

封裝 RCL 時，[ *wwwroot* ] 資料夾中的所有隨附資產都會自動包含在套件中。

使用 `dotnet pack` 命令，而不是 NuGet.exe 版本 `nuget pack` 。

### <a name="exclude-static-assets"></a>排除靜態資產

若要排除靜態資產，請將所需的排除路徑新增至 `$(DefaultItemExcludes)` 專案檔中的屬性群組。 以分號 () 分隔專案 `;` 。

在下列範例中，[ *wwwroot* ] 資料夾中的 *lib* 樣式表單不會被視為靜態資產，且不會包含在已發佈的 RCL 中：

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Typescript 整合

若要在 RCL 中包含 TypeScript 檔案：

1. 將 TypeScript *檔案 ()* 在 *wwwroot* 資料夾之外。 例如，將檔案放在 *用戶端* 資料夾中。

1. 設定 *wwwroot* 資料夾的 TypeScript 組建輸出。 在專案檔中，于的 `TypescriptOutDir` 內設定屬性 `PropertyGroup` ：

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. `ResolveCurrentProjectStaticWebAssets`在專案檔中的內新增下列目標，以將 TypeScript 目標納入為目標的相依性 `PropertyGroup` ：

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>使用參考 RCL 中的內容

RCL 的 *wwwroot* 資料夾中包含的檔案會公開給 RCL 或使用中的應用程式（前置詞下） `_content/{LIBRARY NAME}/` 。 例如，名為的程式庫 *Razor 。類別 .Lib* 會產生靜態內容的路徑 `_content/Razor.Class.Lib/` 。 當產生 NuGet 封裝且元件名稱與封裝識別碼不同時，請使用的封裝識別碼 `{LIBRARY NAME}` 。

取用應用程式會參考程式庫透過 `<script>` 、 `<style>` 、 `<img>` 和其他 HTML 標籤所提供的靜態資產。 使用中的應用程式必須在中啟用 [靜態檔案支援](xref:fundamentals/static-files) `Startup.Configure` ：

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

從組建輸出 () 執行取用應用程式時 `dotnet run` ，預設會在開發環境中啟用靜態 web 資產。 若要在從組建輸出執行時支援其他環境中的資產，請在 `UseStaticWebAssets` *Program.cs* 的主機建立器上呼叫：

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

`UseStaticWebAssets`從已發行的輸出 () 執行應用程式時，不需要呼叫 `dotnet publish` 。

### <a name="multi-project-development-flow"></a>多專案開發流程

使用中的應用程式執行時：

* RCL 中的資產會留在其原始檔案夾中。 資產不會移至使用中的應用程式。
* RCL 的 *wwwroot* 資料夾內的任何變更，都會在重建 RCL 之後，而不重建取用應用程式時，反映在取用應用程式中。

建立 RCL 時，會產生資訊清單來描述靜態 web 資產位置。 取用應用程式會在執行時間讀取資訊清單，以取用參考專案和套件中的資產。 當新的資產新增至 RCL 時，必須重建 RCL，才能在取用應用程式存取新資產之前更新其資訊清單。

### <a name="publish"></a>發佈

當應用程式發佈時，所有參考專案和套件中的隨附資產都會複製到下所發佈應用程式的 [ *wwwroot* ] 資料夾中 `_content/{LIBRARY NAME}/` 。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor您可以將視圖、頁面、控制器、頁面模型、 [ Razor 元件](xref:blazor/components/class-libraries)、[視圖元件](xref:mvc/views/view-components)和資料模型內建至 Razor 類別庫 (RCL) 。 RCL 可以封裝和重複使用。 應用程式可以包括 RCL，以及覆寫它所包含的檢視和頁面。 當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>建立包含 UI 的類別庫 Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 Visual Studio 的 [檔案] 功能表中，選取 [新增]**[專案]** >  。
* 選取 **ASP.NET Core Web 應用程式**。
* 將程式庫命名 (例如 " Razor ClassLib" ) > **確定]**。 若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。
* 確認已選取 **ASP.NET Core 2.1** 或更新版本。
* 選取 [ **Razor 類別庫** > **確定]**。

RCL 具有下列專案檔案：

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

從命令列執行 `dotnet new razorclasslib`。 例如：

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

如需詳細資訊，請參閱 [dotnet new](/dotnet/core/tools/dotnet-new)。 若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。

---

將檔案新增 Razor 至 RCL。

ASP.NET Core 範本會假設 RCL 內容位於 [ *區域* ] 資料夾中。 請參閱 [RCL 頁面版面](#rcl-pages-layout) 配置來建立可公開內容的 RCL， `~/Pages` 而不是 `~/Areas/Pages` 。

## <a name="reference-rcl-content"></a>參考 RCL 內容

RCL 可以由下列各項參考：

* NuGet 套件。 請參閱[建立 NuGet 套件](/nuget/create-packages/creating-a-package)和 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 和[建立和發佈 NuGet 套件](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。
* *{ProjectName}.csproj*。 請參閱 [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)。

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>逐步解說：建立 RCL 專案並使用於 Razor 頁面專案中

您可以下載[完整專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)並測試它，而不是建立它。 下載範例包含額外的程式碼和連結，讓您輕鬆地測試專案。 您可以在[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/6098)留下意見反應以及您對下載範例與逐步指示的評論。

### <a name="test-the-download-app"></a>測試下載應用程式

如果您尚未下載已完成的應用程式，而是要建立逐步解說專案，請跳至[下一節](#create-an-rcl)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio 中開啟 *.sln* 檔案。 執行應用程式。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

從命令提示字元中在 *cli* 目錄中，建置 RCL 和 Web 應用程式。

```dotnetcli
dotnet build
```

移至 *WebApp1* 目錄並執行應用程式：

```dotnetcli
dotnet run
```

---

遵循[測試 WebApp1](#test-webapp1) 中的指示執行。

## <a name="create-an-rcl"></a>建立 RCL

本節會建立 RCL。 Razor 檔案會新增至 RCL。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

建立 RCL 專案：

* 從 Visual Studio 的 [檔案] 功能表中，選取 [新增]**[專案]** >  。
* 選取 **ASP.NET Core Web 應用程式**。
* 將應用程式命名為 **Razor UIClassLib** > **OK**。
* 確認已選取 **ASP.NET Core 2.1** 或更新版本。
* 選取 [ **Razor 類別庫** > **確定]**。
* 加入 Razor 名為 *Razor UIClassLib/Areas/MyFeature/Pages/Shared/_Message* 的部分視圖檔案。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

從命令列執行下列命令：

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

上述命令：

* 建立 `RazorUIClassLib` RCL。
* 建立 Razor _Message 頁面，並將它新增至 RCL。 `-np` 參數會建立頁面而不使用 `PageModel`。
* 建立 [_ViewStart 的 cshtml](xref:mvc/views/layout#running-code-before-each-view) 檔案，並將它新增至 RCL。

需要 *_ViewStart cshtml* 檔案，才能使用在 Razor 下一節) 中新增的頁面專案 (的版面配置。

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>將檔案 Razor 和資料夾加入至專案

* 將 *Razor UIClassLib/Areas/MyFeature/Pages/Shared/_Message* 中的標記取代為下列程式碼：

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* 將 *Razor UIClassLib/Areas/MyFeature/Pages/Page1. cshtml* 中的標記取代為下列程式碼：

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 才能使用部分檢視 (`<partial name="_Message" />`)。 您可以新增 *_ViewImports.cshtml* 檔案，而不要包含 `@addTagHelper` 指示詞。 例如：

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  如需有關 _ViewImports 的詳細資訊，請參閱匯 [入共用](xref:mvc/views/layout#importing-shared-directives)指示詞 *。*

* 建置類別庫，以確認沒有任何編譯器錯誤：

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

組建輸出包含 *RazorUIClassLib.dll* 和 *RazorUIClassLib.Views.dll*。 *RazorUIClassLib.Views.dll* 包含已編譯的 Razor 內容。

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>使用 Razor 頁面專案的 UI 程式庫 Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

建立 Razor 頁面 web 應用程式：

* 在 [方案總管] 中，以滑鼠右鍵按一下解決方案 > [新增]**[新增專案]** >  。
* 選取 **ASP.NET Core Web 應用程式**。
* 將應用程式命名為 **WebApp1**。
* 確認已選取 **ASP.NET Core 2.1** 或更新版本。
* 選取 [Web 應用程式]**[確定]** > 。

* 從 [方案總管]，以滑鼠右鍵按一下 **WebApp1**，然後選取 [設定為啟始專案]。
* 從方案總管，以滑鼠右鍵按一下 **WebApp1**，然後選取 [建置相依性]**[專案相依性]** > 。
* 檢查 **Razor UIClassLib** 是否為 **WebApp1** 的相依性。
* 從 [方案總管]，以滑鼠右鍵按一下 **WebApp1**，然後選取 [新增]**[參考]** > 。
* 在 [**參考管理員**] 對話方塊中，檢查 **Razor UIClassLib** > **確定**。

執行應用程式。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

建立 Razor 頁面 web 應用程式和包含 Razor 頁面應用程式和 RCL 的方案檔：

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

建置和執行 Web 應用程式：

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>測試 WebApp1

流覽至， `/MyFeature/Page1` 確認 Razor UI 類別庫正在使用中。

## <a name="override-views-partial-views-and-pages"></a>覆寫檢視、部分檢視和頁面

當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。 例如，將 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml* 新增至 WebApp1，WebApp1 中的 page1 將優先于 RCL 中的 page1。

在下載範例中，將 *WebApp1/Areas/MyFeature2* 重新命名為 *WebApp1/Areas/MyFeature* 以測試優先順序。

將 *Razor UIClassLib/Areas/MyFeature/Pages/shared/_Message. cshtml* partial View 複製到 *WebApp1/Areas/MyFeature/pages/shared/_Message cshtml*。 更新標記來指示新的位置。 建置並執行應用程式，以確認正在使用部分檢視的應用程式版本。

### <a name="rcl-pages-layout"></a>RCL 頁面版面配置

若要參考 RCL 內容（如同 web 應用程式的 *Pages* 資料夾的一部分），請使用下列檔案結構來建立 RCL 專案：

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

假設 *Razor UIClassLib/Pages/Shared* 包含兩個部分檔案： *_Header cshtml* 和 *_Footer. cshtml*。 `<partial>`標記可新增至 _Layout 的 *cshtml* 檔案：

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>其他資源

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
