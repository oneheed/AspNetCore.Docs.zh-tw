---
title: Razor具有 ASP.NET Core 的類別庫中可重複使用的 UI
author: Rick-Anderson
description: '說明如何 Razor 在 ASP.NET Core 的類別庫中，使用部分視圖建立可重複使用的 UI。'
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: e87e74533fe6900d8e0a73708ad24b765a968493
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056799"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="17bf0-103">使用 Razor ASP.NET Core 中的類別庫專案建立可重複使用的 UI</span><span class="sxs-lookup"><span data-stu-id="17bf0-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="17bf0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="17bf0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17bf0-105">Razor您可以將視圖、頁面、控制器、頁面模型、 [ Razor 元件](xref:blazor/components/class-libraries)、[視圖元件](xref:mvc/views/view-components)和資料模型內建至 Razor 類別庫 (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="17bf0-106">RCL 可以封裝和重複使用。</span><span class="sxs-lookup"><span data-stu-id="17bf0-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="17bf0-107">應用程式可以包括 RCL，以及覆寫它所包含的檢視和頁面。</span><span class="sxs-lookup"><span data-stu-id="17bf0-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="17bf0-108">當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="17bf0-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="17bf0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="17bf0-110">建立包含 UI 的類別庫 Razor</span><span class="sxs-lookup"><span data-stu-id="17bf0-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bf0-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bf0-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="17bf0-112">從 Visual Studio 選取 [ **建立新專案** ]。</span><span class="sxs-lookup"><span data-stu-id="17bf0-112">From Visual Studio select **Create new a new project** .</span></span>
* <span data-ttu-id="17bf0-113">選取 [ **Razor 類別庫** > **下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-113">Select **Razor Class Library** > **Next** .</span></span>
* <span data-ttu-id="17bf0-114">將程式庫命名 (例如，" Razor ClassLib" ) > **建立** ]。</span><span class="sxs-lookup"><span data-stu-id="17bf0-114">Name the library (for example, "RazorClassLib"), > **Create** .</span></span> <span data-ttu-id="17bf0-115">若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="17bf0-116">如果您需要支援 views，請選取 [ **支援頁面和視圖** ]。</span><span class="sxs-lookup"><span data-stu-id="17bf0-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="17bf0-117">依預設，只 Razor 支援頁面。</span><span class="sxs-lookup"><span data-stu-id="17bf0-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="17bf0-118">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="17bf0-118">Select **Create** .</span></span>

<span data-ttu-id="17bf0-119">Razor依預設，類別庫 (RCL) 範本預設為 Razor 元件開發。</span><span class="sxs-lookup"><span data-stu-id="17bf0-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="17bf0-120">**支援頁面和 views** 選項支援頁面和視圖。</span><span class="sxs-lookup"><span data-stu-id="17bf0-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="17bf0-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="17bf0-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="17bf0-122">從命令列執行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="17bf0-123">例如：</span><span class="sxs-lookup"><span data-stu-id="17bf0-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="17bf0-124">Razor依預設，類別庫 (RCL) 範本預設為 Razor 元件開發。</span><span class="sxs-lookup"><span data-stu-id="17bf0-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="17bf0-125">將 `--support-pages-and-views` 選項傳遞 (`dotnet new razorclasslib --support-pages-and-views`) ，以提供頁面和視圖的支援。</span><span class="sxs-lookup"><span data-stu-id="17bf0-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="17bf0-126">如需詳細資訊，請參閱 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="17bf0-127">若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="17bf0-128">將檔案新增 Razor 至 RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="17bf0-129">ASP.NET Core 範本會假設 RCL 內容位於 [ *區域* ] 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="17bf0-130">請參閱 [RCL 頁面版面](#rcl-pages-layout) 配置來建立可公開內容的 RCL， `~/Pages` 而不是 `~/Areas/Pages` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="17bf0-131">參考 RCL 內容</span><span class="sxs-lookup"><span data-stu-id="17bf0-131">Reference RCL content</span></span>

<span data-ttu-id="17bf0-132">RCL 可以由下列各項參考：</span><span class="sxs-lookup"><span data-stu-id="17bf0-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="17bf0-133">NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="17bf0-133">NuGet package.</span></span> <span data-ttu-id="17bf0-134">請參閱[建立 NuGet 套件](/nuget/create-packages/creating-a-package)和 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 和[建立和發佈 NuGet 套件](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="17bf0-135">*{ProjectName}.csproj* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-135">*{ProjectName}.csproj* .</span></span> <span data-ttu-id="17bf0-136">請參閱 [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="17bf0-137">覆寫檢視、部分檢視和頁面</span><span class="sxs-lookup"><span data-stu-id="17bf0-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="17bf0-138">當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="17bf0-139">例如，將 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml* 新增至 WebApp1，WebApp1 中的 page1 將優先于 RCL 中的 page1。</span><span class="sxs-lookup"><span data-stu-id="17bf0-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="17bf0-140">在下載範例中，將 *WebApp1/Areas/MyFeature2* 重新命名為 *WebApp1/Areas/MyFeature* 以測試優先順序。</span><span class="sxs-lookup"><span data-stu-id="17bf0-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="17bf0-141">將 *Razor UIClassLib/Areas/MyFeature/Pages/shared/_Message. cshtml* partial View 複製到 *WebApp1/Areas/MyFeature/pages/shared/_Message cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* .</span></span> <span data-ttu-id="17bf0-142">更新標記來指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="17bf0-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="17bf0-143">建置並執行應用程式，以確認正在使用部分檢視的應用程式版本。</span><span class="sxs-lookup"><span data-stu-id="17bf0-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="17bf0-144">RCL 頁面版面配置</span><span class="sxs-lookup"><span data-stu-id="17bf0-144">RCL Pages layout</span></span>

<span data-ttu-id="17bf0-145">若要參考 RCL 內容（如同 web 應用程式的 *Pages* 資料夾的一部分），請使用下列檔案結構來建立 RCL 專案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="17bf0-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="17bf0-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="17bf0-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="17bf0-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="17bf0-148">假設 *Razor UIClassLib/Pages/Shared* 包含兩個部分檔案： *_Header cshtml* 和 *_Footer. cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml* .</span></span> <span data-ttu-id="17bf0-149">`<partial>`標記可新增至 _Layout 的 *cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="17bf0-150">建立具有靜態資產的 RCL</span><span class="sxs-lookup"><span data-stu-id="17bf0-150">Create an RCL with static assets</span></span>

<span data-ttu-id="17bf0-151">RCL 可能需要隨附的靜態資產，可由 RCL 或 RCL 的取用應用程式參考。</span><span class="sxs-lookup"><span data-stu-id="17bf0-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="17bf0-152">ASP.NET Core 可讓您建立 RCLs，其中包含可供取用應用程式使用的靜態資產。</span><span class="sxs-lookup"><span data-stu-id="17bf0-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="17bf0-153">若要在 RCL 中包含隨附的資產，請在類別庫中建立 *wwwroot* 資料夾，並在該資料夾中包含任何必要的檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="17bf0-154">封裝 RCL 時，[ *wwwroot* ] 資料夾中的所有隨附資產都會自動包含在套件中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="17bf0-155">使用 `dotnet pack` 命令，而不是 NuGet.exe 版本 `nuget pack` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="17bf0-156">排除靜態資產</span><span class="sxs-lookup"><span data-stu-id="17bf0-156">Exclude static assets</span></span>

<span data-ttu-id="17bf0-157">若要排除靜態資產，請將所需的排除路徑新增至 `$(DefaultItemExcludes)` 專案檔中的屬性群組。</span><span class="sxs-lookup"><span data-stu-id="17bf0-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="17bf0-158">以分號 () 分隔專案 `;` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="17bf0-159">在下列範例中，[ *wwwroot* ] 資料夾中的 *lib* 樣式表單不會被視為靜態資產，且不會包含在已發佈的 RCL 中：</span><span class="sxs-lookup"><span data-stu-id="17bf0-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="17bf0-160">Typescript 整合</span><span class="sxs-lookup"><span data-stu-id="17bf0-160">Typescript integration</span></span>

<span data-ttu-id="17bf0-161">若要在 RCL 中包含 TypeScript 檔案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="17bf0-162">將 TypeScript *檔案 ()* 在 *wwwroot* 資料夾之外。</span><span class="sxs-lookup"><span data-stu-id="17bf0-162">Place the TypeScript files ( *.ts* ) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="17bf0-163">例如，將檔案放在 *用戶端* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="17bf0-164">設定 *wwwroot* 資料夾的 TypeScript 組建輸出。</span><span class="sxs-lookup"><span data-stu-id="17bf0-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="17bf0-165">在專案檔中，于的 `TypescriptOutDir` 內設定屬性 `PropertyGroup` ：</span><span class="sxs-lookup"><span data-stu-id="17bf0-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="17bf0-166">`ResolveCurrentProjectStaticWebAssets`在專案檔中的內新增下列目標，以將 TypeScript 目標納入為目標的相依性 `PropertyGroup` ：</span><span class="sxs-lookup"><span data-stu-id="17bf0-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="17bf0-167">使用參考 RCL 中的內容</span><span class="sxs-lookup"><span data-stu-id="17bf0-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="17bf0-168">RCL 的 *wwwroot* 資料夾中包含的檔案會公開給 RCL 或使用中的應用程式（前置詞下） `_content/{LIBRARY NAME}/` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="17bf0-169">例如，名為的程式庫 *Razor 。類別 .Lib* 會產生靜態內容的路徑 `_content/Razor.Class.Lib/` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="17bf0-170">當產生 NuGet 封裝且元件名稱與封裝識別碼不同時，請使用的封裝識別碼 `{LIBRARY NAME}` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="17bf0-171">取用應用程式會參考程式庫透過 `<script>` 、 `<style>` 、 `<img>` 和其他 HTML 標籤所提供的靜態資產。</span><span class="sxs-lookup"><span data-stu-id="17bf0-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="17bf0-172">使用中的應用程式必須在中啟用 [靜態檔案支援](xref:fundamentals/static-files) `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="17bf0-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="17bf0-173">從組建輸出 () 執行取用應用程式時 `dotnet run` ，預設會在開發環境中啟用靜態 web 資產。</span><span class="sxs-lookup"><span data-stu-id="17bf0-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="17bf0-174">若要在從組建輸出執行時支援其他環境中的資產，請在 `UseStaticWebAssets` *Program.cs* 的主機建立器上呼叫：</span><span class="sxs-lookup"><span data-stu-id="17bf0-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs* :</span></span>

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

<span data-ttu-id="17bf0-175">`UseStaticWebAssets`從已發行的輸出 () 執行應用程式時，不需要呼叫 `dotnet publish` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="17bf0-176">多專案開發流程</span><span class="sxs-lookup"><span data-stu-id="17bf0-176">Multi-project development flow</span></span>

<span data-ttu-id="17bf0-177">使用中的應用程式執行時：</span><span class="sxs-lookup"><span data-stu-id="17bf0-177">When the consuming app runs:</span></span>

* <span data-ttu-id="17bf0-178">RCL 中的資產會留在其原始檔案夾中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="17bf0-179">資產不會移至使用中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="17bf0-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="17bf0-180">RCL 的 *wwwroot* 資料夾內的任何變更，都會在重建 RCL 之後，而不重建取用應用程式時，反映在取用應用程式中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="17bf0-181">建立 RCL 時，會產生資訊清單來描述靜態 web 資產位置。</span><span class="sxs-lookup"><span data-stu-id="17bf0-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="17bf0-182">取用應用程式會在執行時間讀取資訊清單，以取用參考專案和套件中的資產。</span><span class="sxs-lookup"><span data-stu-id="17bf0-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="17bf0-183">當新的資產新增至 RCL 時，必須重建 RCL，才能在取用應用程式存取新資產之前更新其資訊清單。</span><span class="sxs-lookup"><span data-stu-id="17bf0-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="17bf0-184">發佈</span><span class="sxs-lookup"><span data-stu-id="17bf0-184">Publish</span></span>

<span data-ttu-id="17bf0-185">當應用程式發佈時，所有參考專案和套件中的隨附資產都會複製到下所發佈應用程式的 [ *wwwroot* ] 資料夾中 `_content/{LIBRARY NAME}/` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17bf0-186">Razor您可以將視圖、頁面、控制器、頁面模型、 [ Razor 元件](xref:blazor/components/class-libraries)、[視圖元件](xref:mvc/views/view-components)和資料模型內建至 Razor 類別庫 (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="17bf0-187">RCL 可以封裝和重複使用。</span><span class="sxs-lookup"><span data-stu-id="17bf0-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="17bf0-188">應用程式可以包括 RCL，以及覆寫它所包含的檢視和頁面。</span><span class="sxs-lookup"><span data-stu-id="17bf0-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="17bf0-189">當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="17bf0-190">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="17bf0-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="17bf0-191">建立包含 UI 的類別庫 Razor</span><span class="sxs-lookup"><span data-stu-id="17bf0-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bf0-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bf0-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="17bf0-193">從 Visual Studio 的 [檔案]  功能表中，選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-193">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="17bf0-194">選取 **ASP.NET Core Web 應用程式** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-194">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="17bf0-195">將程式庫命名 (例如 " Razor ClassLib" ) > **確定]** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-195">Name the library (for example, "RazorClassLib") > **OK** .</span></span> <span data-ttu-id="17bf0-196">若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="17bf0-197">確認已選取 **ASP.NET Core 2.1** 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="17bf0-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="17bf0-198">選取 [ **Razor 類別庫** > **確定]** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-198">Select **Razor Class Library** > **OK** .</span></span>

<span data-ttu-id="17bf0-199">RCL 具有下列專案檔案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="17bf0-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="17bf0-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="17bf0-201">從命令列執行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="17bf0-202">例如：</span><span class="sxs-lookup"><span data-stu-id="17bf0-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="17bf0-203">如需詳細資訊，請參閱 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="17bf0-204">若要避免與產生的檢視程式庫發生檔案名稱衝突，程式庫名稱結尾請務必不要使用 `.Views`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="17bf0-205">將檔案新增 Razor 至 RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="17bf0-206">ASP.NET Core 範本會假設 RCL 內容位於 [ *區域* ] 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="17bf0-207">請參閱 [RCL 頁面版面](#rcl-pages-layout) 配置來建立可公開內容的 RCL， `~/Pages` 而不是 `~/Areas/Pages` 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="17bf0-208">參考 RCL 內容</span><span class="sxs-lookup"><span data-stu-id="17bf0-208">Reference RCL content</span></span>

<span data-ttu-id="17bf0-209">RCL 可以由下列各項參考：</span><span class="sxs-lookup"><span data-stu-id="17bf0-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="17bf0-210">NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="17bf0-210">NuGet package.</span></span> <span data-ttu-id="17bf0-211">請參閱[建立 NuGet 套件](/nuget/create-packages/creating-a-package)和 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 和[建立和發佈 NuGet 套件](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="17bf0-212">*{ProjectName}.csproj* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-212">*{ProjectName}.csproj* .</span></span> <span data-ttu-id="17bf0-213">請參閱 [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="17bf0-214">逐步解說：建立 RCL 專案並使用於 Razor 頁面專案中</span><span class="sxs-lookup"><span data-stu-id="17bf0-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="17bf0-215">您可以下載[完整專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)並測試它，而不是建立它。</span><span class="sxs-lookup"><span data-stu-id="17bf0-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="17bf0-216">下載範例包含額外的程式碼和連結，讓您輕鬆地測試專案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="17bf0-217">您可以在[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/6098)留下意見反應以及您對下載範例與逐步指示的評論。</span><span class="sxs-lookup"><span data-stu-id="17bf0-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="17bf0-218">測試下載應用程式</span><span class="sxs-lookup"><span data-stu-id="17bf0-218">Test the download app</span></span>

<span data-ttu-id="17bf0-219">如果您尚未下載已完成的應用程式，而是要建立逐步解說專案，請跳至[下一節](#create-an-rcl)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bf0-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bf0-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bf0-221">在 Visual Studio 中開啟 *.sln* 檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="17bf0-222">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="17bf0-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="17bf0-223">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="17bf0-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="17bf0-224">從命令提示字元中在 *cli* 目錄中，建置 RCL 和 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="17bf0-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="17bf0-225">移至 *WebApp1* 目錄並執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="17bf0-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="17bf0-226">遵循[測試 WebApp1](#test-webapp1) 中的指示執行。</span><span class="sxs-lookup"><span data-stu-id="17bf0-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="17bf0-227">建立 RCL</span><span class="sxs-lookup"><span data-stu-id="17bf0-227">Create an RCL</span></span>

<span data-ttu-id="17bf0-228">本節會建立 RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-228">In this section, an RCL is created.</span></span> <span data-ttu-id="17bf0-229">Razor 檔案會新增至 RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bf0-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bf0-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bf0-231">建立 RCL 專案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-231">Create the RCL project:</span></span>

* <span data-ttu-id="17bf0-232">從 Visual Studio 的 [檔案]  功能表中，選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-232">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="17bf0-233">選取 **ASP.NET Core Web 應用程式** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-233">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="17bf0-234">將應用程式命名為 **Razor UIClassLib** > **OK** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-234">Name the app **RazorUIClassLib** > **OK** .</span></span>
* <span data-ttu-id="17bf0-235">確認已選取 **ASP.NET Core 2.1** 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="17bf0-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="17bf0-236">選取 [ **Razor 類別庫** > **確定]** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-236">Select **Razor Class Library** > **OK** .</span></span>
* <span data-ttu-id="17bf0-237">加入 Razor 名為 *Razor UIClassLib/Areas/MyFeature/Pages/Shared/_Message* 的部分視圖檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="17bf0-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="17bf0-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="17bf0-239">從命令列執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="17bf0-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="17bf0-240">上述命令：</span><span class="sxs-lookup"><span data-stu-id="17bf0-240">The preceding commands:</span></span>

* <span data-ttu-id="17bf0-241">建立 `RazorUIClassLib` RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="17bf0-242">建立 Razor _Message 頁面，並將它新增至 RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="17bf0-243">`-np` 參數會建立頁面而不使用 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="17bf0-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="17bf0-244">建立 [_ViewStart 的 cshtml](xref:mvc/views/layout#running-code-before-each-view) 檔案，並將它新增至 RCL。</span><span class="sxs-lookup"><span data-stu-id="17bf0-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="17bf0-245">需要 *_ViewStart cshtml* 檔案，才能使用在 Razor 下一節) 中新增的頁面專案 (的版面配置。</span><span class="sxs-lookup"><span data-stu-id="17bf0-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="17bf0-246">將檔案 Razor 和資料夾加入至專案</span><span class="sxs-lookup"><span data-stu-id="17bf0-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="17bf0-247">將 *Razor UIClassLib/Areas/MyFeature/Pages/Shared/_Message* 中的標記取代為下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="17bf0-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="17bf0-248">將 *Razor UIClassLib/Areas/MyFeature/Pages/Page1. cshtml* 中的標記取代為下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="17bf0-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="17bf0-249">需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 才能使用部分檢視 (`<partial name="_Message" />`)。</span><span class="sxs-lookup"><span data-stu-id="17bf0-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="17bf0-250">您可以新增 *_ViewImports.cshtml* 檔案，而不要包含 `@addTagHelper` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="17bf0-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="17bf0-251">例如：</span><span class="sxs-lookup"><span data-stu-id="17bf0-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="17bf0-252">如需有關 _ViewImports 的詳細資訊，請參閱匯 [入共用](xref:mvc/views/layout#importing-shared-directives)指示詞 *。*</span><span class="sxs-lookup"><span data-stu-id="17bf0-252">For more information on *_ViewImports.cshtml* , see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="17bf0-253">建置類別庫，以確認沒有任何編譯器錯誤：</span><span class="sxs-lookup"><span data-stu-id="17bf0-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="17bf0-254">組建輸出包含 *RazorUIClassLib.dll* 和 *RazorUIClassLib.Views.dll* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll* .</span></span> <span data-ttu-id="17bf0-255">*RazorUIClassLib.Views.dll* 包含已編譯的 Razor 內容。</span><span class="sxs-lookup"><span data-stu-id="17bf0-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="17bf0-256">使用 Razor 頁面專案的 UI 程式庫 Razor</span><span class="sxs-lookup"><span data-stu-id="17bf0-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="17bf0-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17bf0-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17bf0-258">建立 Razor 頁面 web 應用程式：</span><span class="sxs-lookup"><span data-stu-id="17bf0-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="17bf0-259">在 [方案總管]  中，以滑鼠右鍵按一下解決方案 > [新增]  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-259">From **Solution Explorer** , right-click the solution > **Add** >  **New Project** .</span></span>
* <span data-ttu-id="17bf0-260">選取 **ASP.NET Core Web 應用程式** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-260">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="17bf0-261">將應用程式命名為 **WebApp1** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-261">Name the app **WebApp1** .</span></span>
* <span data-ttu-id="17bf0-262">確認已選取 **ASP.NET Core 2.1** 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="17bf0-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="17bf0-263">選取 [Web 應用程式]  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-263">Select **Web Application** > **OK** .</span></span>

* <span data-ttu-id="17bf0-264">從 [方案總管]  ，以滑鼠右鍵按一下  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-264">From **Solution Explorer** , right-click on **WebApp1** and select **Set as StartUp Project** .</span></span>
* <span data-ttu-id="17bf0-265">從方案總管  ，以滑鼠右鍵按一下  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-265">From **Solution Explorer** , right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies** .</span></span>
* <span data-ttu-id="17bf0-266">檢查 **Razor UIClassLib** 是否為 **WebApp1** 的相依性。</span><span class="sxs-lookup"><span data-stu-id="17bf0-266">Check **RazorUIClassLib** as a dependency of **WebApp1** .</span></span>
* <span data-ttu-id="17bf0-267">從 [方案總管]  ，以滑鼠右鍵按一下  。</span><span class="sxs-lookup"><span data-stu-id="17bf0-267">From **Solution Explorer** , right-click on **WebApp1** and select **Add** > **Reference** .</span></span>
* <span data-ttu-id="17bf0-268">在 [ **參考管理員** ] 對話方塊中，檢查 **Razor UIClassLib** > **確定** 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK** .</span></span>

<span data-ttu-id="17bf0-269">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="17bf0-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="17bf0-270">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="17bf0-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="17bf0-271">建立 Razor 頁面 web 應用程式和包含 Razor 頁面應用程式和 RCL 的方案檔：</span><span class="sxs-lookup"><span data-stu-id="17bf0-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="17bf0-272">建置和執行 Web 應用程式：</span><span class="sxs-lookup"><span data-stu-id="17bf0-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="17bf0-273">測試 WebApp1</span><span class="sxs-lookup"><span data-stu-id="17bf0-273">Test WebApp1</span></span>

<span data-ttu-id="17bf0-274">流覽至， `/MyFeature/Page1` 確認 Razor UI 類別庫正在使用中。</span><span class="sxs-lookup"><span data-stu-id="17bf0-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="17bf0-275">覆寫檢視、部分檢視和頁面</span><span class="sxs-lookup"><span data-stu-id="17bf0-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="17bf0-276">當 web 應用程式和 RCL 中都有一個 view、partial view 或 Razor Page 時， Razor web 應用程式中) 的標記會優先使用 (的 *cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="17bf0-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup ( *.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="17bf0-277">例如，將 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml* 新增至 WebApp1，WebApp1 中的 page1 將優先于 RCL 中的 page1。</span><span class="sxs-lookup"><span data-stu-id="17bf0-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="17bf0-278">在下載範例中，將 *WebApp1/Areas/MyFeature2* 重新命名為 *WebApp1/Areas/MyFeature* 以測試優先順序。</span><span class="sxs-lookup"><span data-stu-id="17bf0-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="17bf0-279">將 *Razor UIClassLib/Areas/MyFeature/Pages/shared/_Message. cshtml* partial View 複製到 *WebApp1/Areas/MyFeature/pages/shared/_Message cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* .</span></span> <span data-ttu-id="17bf0-280">更新標記來指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="17bf0-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="17bf0-281">建置並執行應用程式，以確認正在使用部分檢視的應用程式版本。</span><span class="sxs-lookup"><span data-stu-id="17bf0-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="17bf0-282">RCL 頁面版面配置</span><span class="sxs-lookup"><span data-stu-id="17bf0-282">RCL Pages layout</span></span>

<span data-ttu-id="17bf0-283">若要參考 RCL 內容（如同 web 應用程式的 *Pages* 資料夾的一部分），請使用下列檔案結構來建立 RCL 專案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="17bf0-284">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="17bf0-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="17bf0-285">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="17bf0-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="17bf0-286">假設 *Razor UIClassLib/Pages/Shared* 包含兩個部分檔案： *_Header cshtml* 和 *_Footer. cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="17bf0-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml* .</span></span> <span data-ttu-id="17bf0-287">`<partial>`標記可新增至 _Layout 的 *cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="17bf0-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="17bf0-288">其他資源</span><span class="sxs-lookup"><span data-stu-id="17bf0-288">Additional resources</span></span>

* <xref:blazor/components/class-libraries>
