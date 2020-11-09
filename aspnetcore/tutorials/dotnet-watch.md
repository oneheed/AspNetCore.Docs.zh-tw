---
title: 使用檔案監看員開發 ASP.NET Core 應用程式
author: rick-anderson
description: 本教學課程會示範如何在 ASP.NET Core 應用程式中安裝及使用 .NET Core CLI 檔案監看員 (dotnet 監看式) 工具。
ms.author: riande
ms.date: 05/31/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060036"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="e4f92-103">使用檔案監看員開發 ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="e4f92-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="e4f92-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="e4f92-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="e4f92-105">`dotnet watch` 是一種工具，會在來源檔案變更時執行 [.NET Core CLI](/dotnet/core/tools) 命令。</span><span class="sxs-lookup"><span data-stu-id="e4f92-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="e4f92-106">例如，檔案變更會觸發編譯、測試執行或部署。</span><span class="sxs-lookup"><span data-stu-id="e4f92-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="e4f92-107">本教學課程使用現有的 Web API 與兩個端點：一個傳回加總，另一個傳回產品。</span><span class="sxs-lookup"><span data-stu-id="e4f92-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="e4f92-108">本教學課程已修正產品方法的 Bug。</span><span class="sxs-lookup"><span data-stu-id="e4f92-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="e4f92-109">下載 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)。</span><span class="sxs-lookup"><span data-stu-id="e4f92-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="e4f92-110">它包含兩個專案： *WebApp* (ASP.NET Core Web API) 和 *WebAppTests* (Web API 的單元測試)。</span><span class="sxs-lookup"><span data-stu-id="e4f92-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="e4f92-111">在命令殼層中，巡覽至 *WebApp* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="e4f92-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="e4f92-112">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="e4f92-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="e4f92-113">您可以使用 `dotnet run --project <PROJECT>` 來指定要執行的專案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="e4f92-114">例如，從範例應用程式的根目錄執行 `dotnet run --project WebApp` 同時也會執行 *WebApp* 專案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="e4f92-115">主控台輸出會顯示類似如下的訊息 (指出應用程式正在執行，並等待要求)：</span><span class="sxs-lookup"><span data-stu-id="e4f92-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="e4f92-116">在網頁瀏覽器中，瀏覽至 `http://localhost:<port number>/api/math/sum?a=4&b=5`。</span><span class="sxs-lookup"><span data-stu-id="e4f92-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="e4f92-117">您應該會看到 `9` 的結果。</span><span class="sxs-lookup"><span data-stu-id="e4f92-117">You should see the result of `9`.</span></span>

<span data-ttu-id="e4f92-118">瀏覽至產品 API (`http://localhost:<port number>/api/math/product?a=4&b=5`)。</span><span class="sxs-lookup"><span data-stu-id="e4f92-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="e4f92-119">它會傳回 `9`，而非您預期的 `20`。</span><span class="sxs-lookup"><span data-stu-id="e4f92-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="e4f92-120">本教學課程稍後會修正該問題。</span><span class="sxs-lookup"><span data-stu-id="e4f92-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="e4f92-121">將 `dotnet watch` 新增至專案</span><span class="sxs-lookup"><span data-stu-id="e4f92-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="e4f92-122">`dotnet watch` 檔案監看員工具隨附於 .NET Core SDK 2.1.300 版本。</span><span class="sxs-lookup"><span data-stu-id="e4f92-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="e4f92-123">使用舊版的 .NET Core SDK 需要以下的步驟。</span><span class="sxs-lookup"><span data-stu-id="e4f92-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="e4f92-124">將 `Microsoft.DotNet.Watcher.Tools` 套件參考新增至 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="e4f92-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="e4f92-125">執行下列命令來安裝 `Microsoft.DotNet.Watcher.Tools` 套件：</span><span class="sxs-lookup"><span data-stu-id="e4f92-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="e4f92-126">使用 `dotnet watch` 執行 .NET Core CLI 命令</span><span class="sxs-lookup"><span data-stu-id="e4f92-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="e4f92-127">任何 [.NET Core CLI 命令](/dotnet/core/tools#cli-commands)都可以使用 `dotnet watch` 執行。</span><span class="sxs-lookup"><span data-stu-id="e4f92-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="e4f92-128">例如：</span><span class="sxs-lookup"><span data-stu-id="e4f92-128">For example:</span></span>

| <span data-ttu-id="e4f92-129">Command</span><span class="sxs-lookup"><span data-stu-id="e4f92-129">Command</span></span> | <span data-ttu-id="e4f92-130">使用監看式的命令</span><span class="sxs-lookup"><span data-stu-id="e4f92-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="e4f92-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="e4f92-131">dotnet run</span></span> | <span data-ttu-id="e4f92-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="e4f92-132">dotnet watch run</span></span> |
| <span data-ttu-id="e4f92-133">dotnet run-f netcoreapp 3。1</span><span class="sxs-lookup"><span data-stu-id="e4f92-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="e4f92-134">dotnet watch run-f netcoreapp 3。1</span><span class="sxs-lookup"><span data-stu-id="e4f92-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="e4f92-135">dotnet run-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="e4f92-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="e4f92-136">dotnet watch run-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="e4f92-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="e4f92-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="e4f92-137">dotnet test</span></span> | <span data-ttu-id="e4f92-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="e4f92-138">dotnet watch test</span></span> |

<span data-ttu-id="e4f92-139">執行 *WebApp* 資料夾中的 `dotnet watch run`。</span><span class="sxs-lookup"><span data-stu-id="e4f92-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="e4f92-140">主控台輸出指出 `watch` 已啟動。</span><span class="sxs-lookup"><span data-stu-id="e4f92-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="e4f92-141">`dotnet watch run`在 web 應用程式上執行時，會啟動瀏覽器，並在準備好時流覽至應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="e4f92-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="e4f92-142">`dotnet watch` 這會藉由讀取應用程式的主控台輸出，並等候所顯示的就緒訊息來執行此工作 <xref:Microsoft.AspNetCore.WebHost> 。</span><span class="sxs-lookup"><span data-stu-id="e4f92-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="e4f92-143">`dotnet watch` 當瀏覽器偵測到監看檔案的變更時，重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="e4f92-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="e4f92-144">若要這樣做，watch 命令會將中介軟體插入至應用程式，以修改應用程式所建立的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="e4f92-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="e4f92-145">中介軟體會將 JavaScript 腳本區塊新增至頁面，以允許 `dotnet watch` 指示瀏覽器重新整理。</span><span class="sxs-lookup"><span data-stu-id="e4f92-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="e4f92-146">目前，所有監看的檔案（包括靜態內容，例如 *.html* 和 *.css* 檔案）的變更都會導致重建應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4f92-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="e4f92-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="e4f92-147">`dotnet watch`:</span></span>

* <span data-ttu-id="e4f92-148">預設只會監看影響組建的檔案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="e4f92-149">透過 configuration)  (任何額外監看的檔案，仍會導致組建發生。</span><span class="sxs-lookup"><span data-stu-id="e4f92-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="e4f92-150">如需設定的詳細資訊，請參閱本檔中的[dotnet-watch configuration](#dotnet-watch-configuration)</span><span class="sxs-lookup"><span data-stu-id="e4f92-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e4f92-151">您可以使用 `dotnet watch --project <PROJECT>` 來指定要監看的專案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="e4f92-152">例如，從範例應用程式的根目錄執行 `dotnet watch --project WebApp run` 同時也會執行並監看 *WebApp* 專案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="e4f92-153">以 `dotnet watch` 進行變更</span><span class="sxs-lookup"><span data-stu-id="e4f92-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="e4f92-154">請確認 `dotnet watch` 正在執行。</span><span class="sxs-lookup"><span data-stu-id="e4f92-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="e4f92-155">修正 *MathController.cs* 之 `Product` 方法的 Bug，使其傳回產品而非加總：</span><span class="sxs-lookup"><span data-stu-id="e4f92-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="e4f92-156">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-156">Save the file.</span></span> <span data-ttu-id="e4f92-157">主控台輸出指出 `dotnet watch` 已偵測到檔案變更，並重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4f92-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="e4f92-158">驗證 `http://localhost:<port number>/api/math/product?a=4&b=5` 是否傳回正確的結果。</span><span class="sxs-lookup"><span data-stu-id="e4f92-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="e4f92-159">使用 `dotnet watch` 執行測試</span><span class="sxs-lookup"><span data-stu-id="e4f92-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="e4f92-160">將 *MathController.cs* 的 `Product` 方法變更回傳回加總。</span><span class="sxs-lookup"><span data-stu-id="e4f92-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="e4f92-161">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-161">Save the file.</span></span>
1. <span data-ttu-id="e4f92-162">在命令殼層中，瀏覽至 *WebAppTests* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="e4f92-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="e4f92-163">執行 [dotnet restore](/dotnet/core/tools/dotnet-restore)。</span><span class="sxs-lookup"><span data-stu-id="e4f92-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="e4f92-164">執行 `dotnet watch test`。</span><span class="sxs-lookup"><span data-stu-id="e4f92-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="e4f92-165">其輸出指出測試失敗，且監看員正在等候檔案變更：</span><span class="sxs-lookup"><span data-stu-id="e4f92-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="e4f92-166">修正 `Product` 方法程式碼，使其傳回產品。</span><span class="sxs-lookup"><span data-stu-id="e4f92-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="e4f92-167">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-167">Save the file.</span></span>

<span data-ttu-id="e4f92-168">`dotnet watch` 會偵測檔案變更，並重新執行測試。</span><span class="sxs-lookup"><span data-stu-id="e4f92-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="e4f92-169">主控台輸出指出測試成功。</span><span class="sxs-lookup"><span data-stu-id="e4f92-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="e4f92-170">自訂要監看的檔案清單</span><span class="sxs-lookup"><span data-stu-id="e4f92-170">Customize files list to watch</span></span>

<span data-ttu-id="e4f92-171">根據預設，`dotnet-watch` 會追蹤符合下列 Glob 模式的所有檔案：</span><span class="sxs-lookup"><span data-stu-id="e4f92-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="e4f92-172">編輯 *.csproj* 檔案可將更多的項目新增至監看清單。</span><span class="sxs-lookup"><span data-stu-id="e4f92-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="e4f92-173">項目可以個別或使用 Glob 模式指定。</span><span class="sxs-lookup"><span data-stu-id="e4f92-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="e4f92-174">選擇不使用要監看的檔案</span><span class="sxs-lookup"><span data-stu-id="e4f92-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="e4f92-175">`dotnet-watch` 可以設定成忽略其預設設定。</span><span class="sxs-lookup"><span data-stu-id="e4f92-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="e4f92-176">若要忽略特定的檔案，請將 `Watch="false"` 屬性新增至 *.csproj* 檔案的項目定義：</span><span class="sxs-lookup"><span data-stu-id="e4f92-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="e4f92-177">自訂監看式專案</span><span class="sxs-lookup"><span data-stu-id="e4f92-177">Custom watch projects</span></span>

<span data-ttu-id="e4f92-178">`dotnet-watch` 不限制為 C# 專案。</span><span class="sxs-lookup"><span data-stu-id="e4f92-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="e4f92-179">您可以建立自訂的監看式專案來處理不同的案例。</span><span class="sxs-lookup"><span data-stu-id="e4f92-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="e4f92-180">請考慮下列專案配置：</span><span class="sxs-lookup"><span data-stu-id="e4f92-180">Consider the following project layout:</span></span>

* <span data-ttu-id="e4f92-181">**測試**</span><span class="sxs-lookup"><span data-stu-id="e4f92-181">**test/**</span></span>
  * <span data-ttu-id="e4f92-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="e4f92-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="e4f92-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="e4f92-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="e4f92-184">如果目標是監看這兩個專案，請建立設定成監看這兩個專案的自訂專案檔：</span><span class="sxs-lookup"><span data-stu-id="e4f92-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="e4f92-185">若要開始監看兩個專案的檔案，請變更至 *test* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="e4f92-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="e4f92-186">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e4f92-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="e4f92-187">任一測試專案中的任何檔案發生變更時，就會執行 VSTest。</span><span class="sxs-lookup"><span data-stu-id="e4f92-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="e4f92-188">dotnet-監看設定</span><span class="sxs-lookup"><span data-stu-id="e4f92-188">dotnet-watch configuration</span></span>

<span data-ttu-id="e4f92-189">某些設定選項可以 `dotnet watch` 透過環境變數傳遞至。</span><span class="sxs-lookup"><span data-stu-id="e4f92-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="e4f92-190">可用的變數如下：</span><span class="sxs-lookup"><span data-stu-id="e4f92-190">The available variables are:</span></span>

| <span data-ttu-id="e4f92-191">設定</span><span class="sxs-lookup"><span data-stu-id="e4f92-191">Setting</span></span>  | <span data-ttu-id="e4f92-192">描述</span><span class="sxs-lookup"><span data-stu-id="e4f92-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="e4f92-193">如果設定為 "1" 或 "true"，則會使用輪詢檔案監看員， `dotnet watch` 而不是 CoreFx 的 `FileSystemWatcher` 。</span><span class="sxs-lookup"><span data-stu-id="e4f92-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="e4f92-194">用於監看網路共用上的檔案或 Docker 掛接的磁片區。</span><span class="sxs-lookup"><span data-stu-id="e4f92-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="e4f92-195">根據預設，藉 `dotnet watch` 由避免某些作業（例如，在每個檔案變更時執行還原或重新評估監看的檔案集合）來優化組建。</span><span class="sxs-lookup"><span data-stu-id="e4f92-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="e4f92-196">如果設定為 "1" 或 "true"，則會停用這些優化。</span><span class="sxs-lookup"><span data-stu-id="e4f92-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="e4f92-197">`dotnet watch run` 嘗試啟動 web 應用程式的瀏覽器，並在 `launchBrowser` *launchSettings.js* 中設定。</span><span class="sxs-lookup"><span data-stu-id="e4f92-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json* .</span></span> <span data-ttu-id="e4f92-198">如果設定為 "1" 或 "true"，則會隱藏此行為。</span><span class="sxs-lookup"><span data-stu-id="e4f92-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="e4f92-199">`dotnet watch run` 嘗試在偵測到檔案變更時重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="e4f92-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="e4f92-200">如果設定為 "1" 或 "true"，則會隱藏此行為。</span><span class="sxs-lookup"><span data-stu-id="e4f92-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="e4f92-201">如果設定，也會抑制此行為 `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` 。</span><span class="sxs-lookup"><span data-stu-id="e4f92-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="e4f92-202">GitHub 中的 `dotnet-watch`</span><span class="sxs-lookup"><span data-stu-id="e4f92-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="e4f92-203">`dotnet-watch` 是 GitHub [dotnet/AspNetCore 存放庫](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)的一部分。</span><span class="sxs-lookup"><span data-stu-id="e4f92-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
