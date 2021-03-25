---
title: 使用檔案監看員開發 ASP.NET Core 應用程式
author: rick-anderson
description: 本教學課程會示範如何在 ASP.NET Core 應用程式中安裝及使用 .NET Core CLI 檔案監看員 (dotnet 監看式) 工具。
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: e16ca16efd2c4998f486d3fab6bfaaa38fbf6311
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105051084"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>使用檔案監看員開發 ASP.NET Core 應用程式

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` 是一種工具，會在來源檔案變更時執行 [.NET Core CLI](/dotnet/core/tools) 命令。 例如，檔案變更會觸發編譯、測試執行或部署。

本教學課程使用現有的 Web API 與兩個端點：一個傳回加總，另一個傳回產品。 本教學課程已修正產品方法的 Bug。

下載 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/dotnet-watch/sample)。 它包含兩個專案：*WebApp* (ASP.NET Core Web API) 和 *WebAppTests* (Web API 的單元測試)。

在命令殼層中，巡覽至 *WebApp* 資料夾。 執行以下命令：

```dotnetcli
dotnet run
```

> [!NOTE]
> 您可以使用 `dotnet run --project <PROJECT>` 來指定要執行的專案。 例如，從範例應用程式的根目錄執行 `dotnet run --project WebApp` 同時也會執行 *WebApp* 專案。

主控台輸出會顯示類似如下的訊息 (指出應用程式正在執行，並等待要求)：

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

在網頁瀏覽器中，瀏覽至 `http://localhost:<port number>/api/math/sum?a=4&b=5`。 您應該會看到 `9` 的結果。

瀏覽至產品 API (`http://localhost:<port number>/api/math/product?a=4&b=5`)。 它會傳回 `9`，而非您預期的 `20`。 本教學課程稍後會修正該問題。

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>將 `dotnet watch` 新增至專案

`dotnet watch` 檔案監看員工具隨附於 .NET Core SDK 2.1.300 版本。 使用舊版的 .NET Core SDK 需要以下的步驟。

1. 將 `Microsoft.DotNet.Watcher.Tools` 套件參考新增至 *.csproj* 檔案：

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. 執行下列命令來安裝 `Microsoft.DotNet.Watcher.Tools` 套件：

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>使用 `dotnet watch` 執行 .NET Core CLI 命令

任何 [.NET Core CLI 命令](/dotnet/core/tools#cli-commands)都可以使用 `dotnet watch` 執行。 例如：

| 命令 | 使用監看式的命令 |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run-f netcoreapp 3。1 | dotnet watch run-f netcoreapp 3。1 |
| dotnet run-f netcoreapp 3.1----arg1 | dotnet watch run-f netcoreapp 3.1----arg1 |
| dotnet test | dotnet watch test |

執行 *WebApp* 資料夾中的 `dotnet watch run`。 主控台輸出指出 `watch` 已啟動。

::: moniker range=">= aspnetcore-5.0"
`dotnet watch run`在 web 應用程式上執行時，會啟動瀏覽器，並在準備好時流覽至應用程式的 URL。 `dotnet watch` 這會藉由讀取應用程式的主控台輸出，並等候所顯示的就緒訊息來執行此工作 <xref:Microsoft.AspNetCore.WebHost> 。

`dotnet watch` 當瀏覽器偵測到監看檔案的變更時，重新整理瀏覽器。 若要這樣做，watch 命令會將中介軟體插入至應用程式，以修改應用程式所建立的 HTML 回應。 中介軟體會將 JavaScript 腳本區塊新增至頁面，以允許 `dotnet watch` 指示瀏覽器重新整理。 目前，所有監看的檔案（包括靜態內容，例如 *.html* 和 *.css* 檔案）的變更都會導致重建應用程式。

`dotnet watch`:

* 預設只會監看影響組建的檔案。
* 透過 configuration)  (任何額外監看的檔案，仍會導致組建發生。

如需設定的詳細資訊，請參閱本檔中的[dotnet-watch configuration](#dotnet-watch-configuration)

::: moniker-end

> [!NOTE]
> 您可以使用 `dotnet watch --project <PROJECT>` 來指定要監看的專案。 例如，從範例應用程式的根目錄執行 `dotnet watch --project WebApp run` 同時也會執行並監看 *WebApp* 專案。

## <a name="make-changes-with-dotnet-watch"></a>以 `dotnet watch` 進行變更

請確認 `dotnet watch` 正在執行。

修正 *MathController.cs* 之 `Product` 方法的 Bug，使其傳回產品而非加總：

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

儲存檔案。 主控台輸出指出 `dotnet watch` 已偵測到檔案變更，並重新啟動應用程式。

驗證 `http://localhost:<port number>/api/math/product?a=4&b=5` 是否傳回正確的結果。

## <a name="run-tests-using-dotnet-watch"></a>使用 `dotnet watch` 執行測試

1. 將 *MathController.cs* 的 `Product` 方法變更回傳回加總。 儲存檔案。
1. 在命令殼層中，瀏覽至 *WebAppTests* 資料夾。
1. 執行 [dotnet restore](/dotnet/core/tools/dotnet-restore)。
1. 執行 `dotnet watch test`。 其輸出指出測試失敗，且監看員正在等候檔案變更：

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. 修正 `Product` 方法程式碼，使其傳回產品。 儲存檔案。

`dotnet watch` 會偵測檔案變更，並重新執行測試。 主控台輸出指出測試成功。

## <a name="customize-files-list-to-watch"></a>自訂要監看的檔案清單

根據預設，`dotnet-watch` 會追蹤符合下列 Glob 模式的所有檔案：

* `**/*.cs`
* `*.csproj`
* `**/*.resx`
* 內容檔案： `wwwroot/**` 、 `**/*.config` 、 `**/*.json`

編輯 *.csproj* 檔案可將更多的項目新增至監看清單。 項目可以個別或使用 Glob 模式指定。

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>選擇不使用要監看的檔案

`dotnet-watch` 可以設定成忽略其預設設定。 若要忽略特定的檔案，請將 `Watch="false"` 屬性新增至 *.csproj* 檔案的項目定義：

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

```xml
<ItemGroup>
     <!-- Exclude all Content items from being watched. -->
    <Content Update="@(Content)" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>自訂監看式專案

`dotnet-watch` 不限制為 C# 專案。 您可以建立自訂的監看式專案來處理不同的案例。 請考慮下列專案配置：

* **測試**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

如果目標是監看這兩個專案，請建立設定成監看這兩個專案的自訂專案檔：

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

若要開始監看兩個專案的檔案，請變更至 *test* 資料夾。 執行以下命令：

```dotnetcli
dotnet watch msbuild /t:Test
```

任一測試專案中的任何檔案發生變更時，就會執行 VSTest。

## <a name="dotnet-watch-configuration"></a>dotnet-監看設定

某些設定選項可以 `dotnet watch` 透過環境變數傳遞至。 可用的變數如下：

| 設定  | Description |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | 如果設定為 "1" 或 "true"，則會使用輪詢檔案監看員， `dotnet watch` 而不是 CoreFx 的 `FileSystemWatcher` 。 用於監看網路共用上的檔案或 Docker 掛接的磁片區。                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | 根據預設，藉 `dotnet watch` 由避免某些作業（例如，在每個檔案變更時執行還原或重新評估監看的檔案集合）來優化組建。 如果設定為 "1" 或 "true"，則會停用這些優化。 |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | `dotnet watch run` 嘗試啟動 web 應用程式的瀏覽器，並在 `launchBrowser` *launchSettings.js* 中設定。 如果設定為 "1" 或 "true"，則會隱藏此行為。 |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | `dotnet watch run` 嘗試在偵測到檔案變更時重新整理瀏覽器。 如果設定為 "1" 或 "true"，則會隱藏此行為。 如果設定，也會抑制此行為 `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` 。 |
