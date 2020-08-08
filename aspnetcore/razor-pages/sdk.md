---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 瞭解 Razor ASP.NET Core 中的頁面如何讓撰寫以頁面為焦點的案例更輕鬆且更具生產力，而不是使用 MVC。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
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
ms.openlocfilehash: b960460a50558a11bc47f9a1844931aa32e3d696
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021415"
---
# <a name="aspnet-core-no-locrazor-sdk"></a><span data-ttu-id="acbe3-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="acbe3-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="acbe3-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="acbe3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="acbe3-105">概觀</span><span class="sxs-lookup"><span data-stu-id="acbe3-105">Overview</span></span>

<span data-ttu-id="acbe3-106">[!INCLUDE[](~/includes/2.1-SDK.md)]包含 `Microsoft.NET.Sdk.Razor` MSBuild sdk (Razor sdk) 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="acbe3-107">RazorSDK：</span><span class="sxs-lookup"><span data-stu-id="acbe3-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="acbe3-108">需要建立、封裝和發行包含 [Razor](xref:mvc/views/razor) ASP.NET CORE MVC 或專案之檔案的專案 [Blazor](xref:blazor/index) 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="acbe3-109">包含一組預先定義的目標、屬性和專案，可讓您自訂 Razor (*. cshtml*或*razor*) 檔案的編譯。</span><span class="sxs-lookup"><span data-stu-id="acbe3-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="acbe3-110">RazorSDK 包含 `Content` `Include` 屬性設定為 `**\*.cshtml` 和通配模式的專案 `**\*.razor` 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="acbe3-111">已發行相符的檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="acbe3-112">將建立、封裝和發行專案的體驗標準化，其中包含 [Razor](xref:mvc/views/razor) ASP.NET CORE MVC 專案的檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="acbe3-113">包含一組預先定義的目標、屬性和專案，可讓您自訂檔案的編譯 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="acbe3-114">RazorSDK 包含一個 `Content` 專案，並 `Include` 將屬性設定為 `**\*.cshtml` 萬用字元模式。</span><span class="sxs-lookup"><span data-stu-id="acbe3-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="acbe3-115">已發行相符的檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="acbe3-116">必要條件</span><span class="sxs-lookup"><span data-stu-id="acbe3-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a><span data-ttu-id="acbe3-117">使用 Razor SDK</span><span class="sxs-lookup"><span data-stu-id="acbe3-117">Use the Razor SDK</span></span>

<span data-ttu-id="acbe3-118">大部分的 web 應用程式都不需要明確參考 Razor SDK。</span><span class="sxs-lookup"><span data-stu-id="acbe3-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="acbe3-119">若要使用 Razor SDK 來建立包含 Razor 視圖或頁面的類別庫 Razor ，建議您從 Razor 類別庫 (RCL) 專案範本開始。</span><span class="sxs-lookup"><span data-stu-id="acbe3-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="acbe3-120">用來建立 (razor) 檔案的 RCL，至少 Blazor 需要[AspNetCore 元件](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)套件的參考。 *.razor*</span><span class="sxs-lookup"><span data-stu-id="acbe3-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="acbe3-121">用來 Razor (*. cshtml*檔案建立視圖或頁面的 RCL) 至少需要 `netcoreapp3.0` 以或更新版本為目標，並 `FrameworkReference` 在其專案檔中具有[AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="acbe3-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="acbe3-122">若要使用 Razor SDK 來建立包含 Razor 視圖或頁面的類別庫 Razor ：</span><span class="sxs-lookup"><span data-stu-id="acbe3-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="acbe3-123">使用 `Microsoft.NET.Sdk.Razor` 來取代 `Microsoft.NET.Sdk`：</span><span class="sxs-lookup"><span data-stu-id="acbe3-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="acbe3-124">通常，必須要有套件參考， `Microsoft.AspNetCore.Mvc` 才能接收建立和編譯 Razor 頁面和視圖所需的其他相依性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="acbe3-125">您的專案至少應該將套件參考新增至：</span><span class="sxs-lookup"><span data-stu-id="acbe3-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="acbe3-126">`Microsoft.AspNetCore.Razor.Design`封裝會提供 Razor 專案的編譯工作和目標。</span><span class="sxs-lookup"><span data-stu-id="acbe3-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="acbe3-127">`Microsoft.AspNetCore.Mvc` 中包含上述套件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="acbe3-128">下列標記顯示的專案檔會使用 Razor SDK 來建立 Razor ASP.NET Core Razor Pages 應用程式的檔案：</span><span class="sxs-lookup"><span data-stu-id="acbe3-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="acbe3-129">`Microsoft.AspNetCore.Razor.Design`和 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 封裝包含在[AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="acbe3-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="acbe3-130">不過，版本較少的 `Microsoft.AspNetCore.App` 套件參考會針對不包含最新版本的應用程式提供中繼套件 `Microsoft.AspNetCore.Razor.Design` 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="acbe3-131">專案必須參考一致版本的 `Microsoft.AspNetCore.Razor.Design` (或 `Microsoft.AspNetCore.Mvc`) ，才會包含的最新組建時間修正 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="acbe3-132">如需詳細資訊，請參閱[此 GitHub 問題](https://github.com/aspnet/Razor/issues/2553)。</span><span class="sxs-lookup"><span data-stu-id="acbe3-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="acbe3-133">屬性</span><span class="sxs-lookup"><span data-stu-id="acbe3-133">Properties</span></span>

<span data-ttu-id="acbe3-134">下列屬性會將 Razor 的 SDK 行為控制為專案組建的一部分：</span><span class="sxs-lookup"><span data-stu-id="acbe3-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="acbe3-135">`RazorCompileOnBuild`：當時 `true` ，會編譯併發出 Razor 元件，做為建立專案的一部分。</span><span class="sxs-lookup"><span data-stu-id="acbe3-135">`RazorCompileOnBuild`: When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="acbe3-136">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-136">Defaults to `true`.</span></span>
* <span data-ttu-id="acbe3-137">`RazorCompileOnPublish`：當時 `true` ，會編譯併發出 Razor 元件，做為發行專案的一部分。</span><span class="sxs-lookup"><span data-stu-id="acbe3-137">`RazorCompileOnPublish`: When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="acbe3-138">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-138">Defaults to `true`.</span></span>

<span data-ttu-id="acbe3-139">下表中的屬性和專案可用來設定 SDK 的輸入和輸出 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="acbe3-140">從 ASP.NET Core 3.0 開始， Razor 如果 `RazorCompileOnBuild` 專案檔中的或 `RazorCompileOnPublish` MSBuild 屬性已停用，則預設不會提供 MVC 視圖或頁面。</span><span class="sxs-lookup"><span data-stu-id="acbe3-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="acbe3-141">應用程式必須加入 AspNetCore 的明確參考[ Razor 。Microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation)套件（如果應用程式依賴執行時間編譯來處理*cshtml*檔案）。</span><span class="sxs-lookup"><span data-stu-id="acbe3-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="acbe3-142">項目</span><span class="sxs-lookup"><span data-stu-id="acbe3-142">Items</span></span> | <span data-ttu-id="acbe3-143">描述</span><span class="sxs-lookup"><span data-stu-id="acbe3-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="acbe3-144">專案元素 (*. cshtml*檔案) ，這是程式碼產生的輸入。</span><span class="sxs-lookup"><span data-stu-id="acbe3-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="acbe3-145">專案元素 (*razor*檔案) ，這是元件程式 Razor 代碼產生的輸入。</span><span class="sxs-lookup"><span data-stu-id="acbe3-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="acbe3-146">專案元素 (*.cs*檔案) ，這是 Razor 編譯目標的輸入。</span><span class="sxs-lookup"><span data-stu-id="acbe3-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="acbe3-147">使用此 `ItemGroup` 來指定要編譯到元件中的其他檔案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="acbe3-148">用來編寫元件屬性的專案元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="acbe3-149">例如：</span><span class="sxs-lookup"><span data-stu-id="acbe3-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="acbe3-150">做為內嵌資源加入至產生之元件的專案元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="acbe3-151">屬性</span><span class="sxs-lookup"><span data-stu-id="acbe3-151">Property</span></span> | <span data-ttu-id="acbe3-152">描述</span><span class="sxs-lookup"><span data-stu-id="acbe3-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="acbe3-153">檔案名 (，但不包含所產生之元件的副檔名) Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="acbe3-154">Razor輸出目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="acbe3-155">用來判斷用來建立元件的工具組 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="acbe3-156">有效值是 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="acbe3-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="acbe3-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="acbe3-158">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-158">Default is `true`.</span></span> <span data-ttu-id="acbe3-159">當時 `true` ，會包含*web.config*、 *. json*和*cshtml*檔案做為專案中的內容。</span><span class="sxs-lookup"><span data-stu-id="acbe3-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="acbe3-160">透過來參考時 `Microsoft.NET.Sdk.Web` ，也會包含*wwwroot*和 config 檔案底下的檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="acbe3-161">如果是 `true`，請包括來自 `RazorGenerate` 項目之 `Content` 項目的 *.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="acbe3-162">當時 `true` ，會產生包含所指定屬性的 *.cs*檔案 `RazorAssemblyAttribute` ，並在編譯輸出中包含檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="acbe3-163">如果是 `true`，請將一組預設的組件屬性新增至 `RazorAssemblyAttribute`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="acbe3-164">當時 `true` ，會將 `RazorGenerate` 專案 (*. cshtml*) 檔案複製到發行目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="acbe3-165">通常， Razor 如果已發行的應用程式在組建階段或發行時間參與編譯，則不需要檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="acbe3-166">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-166">Defaults to `false`.</span></span> |
| `PreserveCompilationReferences` | <span data-ttu-id="acbe3-167">如果是 `true`，請將參考組件項目複製到發行目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="acbe3-168">一般來說，如果 Razor 在組建時間或發行時間發生編譯，則發行的應用程式不需要參考元件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="acbe3-169">`true`如果您已發佈的應用程式需要執行時間編譯，請設定為。</span><span class="sxs-lookup"><span data-stu-id="acbe3-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="acbe3-170">例如， `true` 如果應用程式在執行時間修改*cshtml*檔案，或使用內嵌的視圖，請將值設定為。</span><span class="sxs-lookup"><span data-stu-id="acbe3-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="acbe3-171">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="acbe3-172">若為 `true` ，則 Razor 會將所有內容專案 (*. cshtml*檔案) 標示為包含在產生的 NuGet 套件中。</span><span class="sxs-lookup"><span data-stu-id="acbe3-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="acbe3-173">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="acbe3-174">當時 `true` ，會將 Razor 產生 (*. cshtml*) 專案當做內嵌檔案加入至產生的 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="acbe3-175">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="acbe3-176">如果是 `true`，請使用持續性組建伺服器處理序來卸載程式碼產生工作。</span><span class="sxs-lookup"><span data-stu-id="acbe3-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="acbe3-177">預設值為 `UseSharedCompilation`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="acbe3-178">當時 `true` ，SDK 會在執行時間產生 MVC 用來執行應用程式元件探索的其他屬性。</span><span class="sxs-lookup"><span data-stu-id="acbe3-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="acbe3-179">要從以 `Content` Web 或 SDK 為目標之專案中的專案群組排除的專案元素的萬用字元模式 Razor</span><span class="sxs-lookup"><span data-stu-id="acbe3-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="acbe3-180">當時 `true` ， *.config*和 *. json*檔案不會複製到組建輸出目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="acbe3-181">當時 `true` ， Razor 會將 SDK 設定為新增建立包含 mvc 視圖或頁面之應用程式時所需的 MVC 設定支援 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="acbe3-182">針對以 Web SDK 為目標的 .NET Core 3.0 或更新版本專案，會以隱含方式設定此屬性。</span><span class="sxs-lookup"><span data-stu-id="acbe3-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="acbe3-183">Razor要設為目標的語言版本。</span><span class="sxs-lookup"><span data-stu-id="acbe3-183">The version of the Razor Language to target.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="acbe3-184">屬性</span><span class="sxs-lookup"><span data-stu-id="acbe3-184">Property</span></span> | <span data-ttu-id="acbe3-185">描述</span><span class="sxs-lookup"><span data-stu-id="acbe3-185">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="acbe3-186">檔案名 (，但不包含所產生之元件的副檔名) Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-186">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="acbe3-187">Razor輸出目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-187">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="acbe3-188">用來判斷用來建立元件的工具組 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-188">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="acbe3-189">有效值是 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-189">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="acbe3-190">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="acbe3-190">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="acbe3-191">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-191">Default is `true`.</span></span> <span data-ttu-id="acbe3-192">當時 `true` ，會包含*web.config*、 *. json*和*cshtml*檔案做為專案中的內容。</span><span class="sxs-lookup"><span data-stu-id="acbe3-192">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="acbe3-193">透過來參考時 `Microsoft.NET.Sdk.Web` ，也會包含*wwwroot*和 config 檔案底下的檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-193">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="acbe3-194">如果是 `true`，請包括來自 `RazorGenerate` 項目之 `Content` 項目的 *.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-194">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="acbe3-195">當時 `true` ，會產生包含所指定屬性的 *.cs*檔案 `RazorAssemblyAttribute` ，並在編譯輸出中包含檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-195">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="acbe3-196">如果是 `true`，請將一組預設的組件屬性新增至 `RazorAssemblyAttribute`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-196">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="acbe3-197">當時 `true` ，會將 `RazorGenerate` 專案 (*. cshtml*) 檔案複製到發行目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-197">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="acbe3-198">通常， Razor 如果已發行的應用程式在組建階段或發行時間參與編譯，則不需要檔案。</span><span class="sxs-lookup"><span data-stu-id="acbe3-198">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="acbe3-199">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-199">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="acbe3-200">如果是 `true`，請將參考組件項目複製到發行目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-200">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="acbe3-201">一般來說，如果 Razor 在組建時間或發行時間發生編譯，則發行的應用程式不需要參考元件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-201">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="acbe3-202">`true`如果您已發佈的應用程式需要執行時間編譯，請設定為。</span><span class="sxs-lookup"><span data-stu-id="acbe3-202">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="acbe3-203">例如， `true` 如果應用程式在執行時間修改*cshtml*檔案，或使用內嵌的視圖，請將值設定為。</span><span class="sxs-lookup"><span data-stu-id="acbe3-203">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="acbe3-204">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-204">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="acbe3-205">若為 `true` ，則 Razor 會將所有內容專案 (*. cshtml*檔案) 標示為包含在產生的 NuGet 套件中。</span><span class="sxs-lookup"><span data-stu-id="acbe3-205">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="acbe3-206">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-206">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="acbe3-207">當時 `true` ，會將 Razor 產生 (*. cshtml*) 專案當做內嵌檔案加入至產生的 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-207">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="acbe3-208">預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-208">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="acbe3-209">如果是 `true`，請使用持續性組建伺服器處理序來卸載程式碼產生工作。</span><span class="sxs-lookup"><span data-stu-id="acbe3-209">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="acbe3-210">預設值為 `UseSharedCompilation`。</span><span class="sxs-lookup"><span data-stu-id="acbe3-210">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="acbe3-211">當時 `true` ，SDK 會在執行時間產生 MVC 用來執行應用程式元件探索的其他屬性。</span><span class="sxs-lookup"><span data-stu-id="acbe3-211">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="acbe3-212">要從以 `Content` Web 或 SDK 為目標之專案中的專案群組排除的專案元素的萬用字元模式 Razor</span><span class="sxs-lookup"><span data-stu-id="acbe3-212">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="acbe3-213">當時 `true` ， *.config*和 *. json*檔案不會複製到組建輸出目錄。</span><span class="sxs-lookup"><span data-stu-id="acbe3-213">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="acbe3-214">當時 `true` ， Razor 會將 SDK 設定為新增建立包含 mvc 視圖或頁面之應用程式時所需的 MVC 設定支援 Razor 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-214">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="acbe3-215">針對以 Web SDK 為目標的 .NET Core 3.0 或更新版本專案，會以隱含方式設定此屬性。</span><span class="sxs-lookup"><span data-stu-id="acbe3-215">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="acbe3-216">Razor要設為目標的語言版本。</span><span class="sxs-lookup"><span data-stu-id="acbe3-216">The version of the Razor Language to target.</span></span> |

::: moniker-end

<span data-ttu-id="acbe3-217">如需屬性的詳細資訊，請參閱[MSBuild 屬性](/visualstudio/msbuild/msbuild-properties)。</span><span class="sxs-lookup"><span data-stu-id="acbe3-217">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="acbe3-218">目標</span><span class="sxs-lookup"><span data-stu-id="acbe3-218">Targets</span></span>

<span data-ttu-id="acbe3-219">RazorSDK 會定義兩個主要目標：</span><span class="sxs-lookup"><span data-stu-id="acbe3-219">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="acbe3-220">`RazorGenerate`：程式碼會從專案元素產生 *.cs*檔案 `RazorGenerate` 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-220">`RazorGenerate`: Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="acbe3-221">使用 `RazorGenerateDependsOn` 屬性來指定可在此目標之前或之後執行的其他目標。</span><span class="sxs-lookup"><span data-stu-id="acbe3-221">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="acbe3-222">`RazorCompile`：將產生的 *.cs*檔案編譯成 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-222">`RazorCompile`: Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="acbe3-223">使用 `RazorCompileDependsOn` 來指定可在此目標之前或之後執行的其他目標。</span><span class="sxs-lookup"><span data-stu-id="acbe3-223">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="acbe3-224">`RazorComponentGenerate`：程式碼會產生專案元素的 *.cs*檔案 `RazorComponent` 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-224">`RazorComponentGenerate`: Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="acbe3-225">使用 `RazorComponentGenerateDependsOn` 屬性來指定可在此目標之前或之後執行的其他目標。</span><span class="sxs-lookup"><span data-stu-id="acbe3-225">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-no-locrazor-views"></a><span data-ttu-id="acbe3-226">執行時間編譯的 Razor 視圖</span><span class="sxs-lookup"><span data-stu-id="acbe3-226">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="acbe3-227">根據預設， Razor SDK 不會發佈執行執行時間編譯所需的參考元件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-227">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="acbe3-228">當應用程式模型依賴執行階段編譯時，這會導致編譯失敗&mdash;例如，發佈應用程式之後，應用程式使用內嵌的檢視或變更檢視。</span><span class="sxs-lookup"><span data-stu-id="acbe3-228">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="acbe3-229">將 `CopyRefAssembliesToPublishDirectory` 設為 `true` 以繼續發佈參考組件。</span><span class="sxs-lookup"><span data-stu-id="acbe3-229">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="acbe3-230">針對 web 應用程式，請確定您的應用程式是以 SDK 為目標 `Microsoft.NET.Sdk.Web` 。</span><span class="sxs-lookup"><span data-stu-id="acbe3-230">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="no-locrazor-language-version"></a><span data-ttu-id="acbe3-231">Razor語言版本</span><span class="sxs-lookup"><span data-stu-id="acbe3-231">Razor language version</span></span>

<span data-ttu-id="acbe3-232">以 SDK 為目標時 `Microsoft.NET.Sdk.Web` ， Razor 會從應用程式的目標 framework 版本推斷語言版本。</span><span class="sxs-lookup"><span data-stu-id="acbe3-232">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="acbe3-233">針對以 SDK 為目標的專案 `Microsoft.NET.Sdk.Razor` ，或在罕見的情況下，應用程式需要的 Razor 語言版本與推斷的值不同，您可以 `<RazorLangVersion>` 在應用程式的專案檔中設定屬性來設定版本：</span><span class="sxs-lookup"><span data-stu-id="acbe3-233">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="acbe3-234">Razor的語言版本與所建立之執行時間的版本緊密整合。</span><span class="sxs-lookup"><span data-stu-id="acbe3-234">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="acbe3-235">不支援以不是針對執行時間設計的語言版本為目標，而且可能會產生組建錯誤。</span><span class="sxs-lookup"><span data-stu-id="acbe3-235">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acbe3-236">其他資源</span><span class="sxs-lookup"><span data-stu-id="acbe3-236">Additional resources</span></span>

* [<span data-ttu-id="acbe3-237">適用於 .NET Core 之 csproj 格式的新增項目</span><span class="sxs-lookup"><span data-stu-id="acbe3-237">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="acbe3-238">一般 MSBuild 專案項目</span><span class="sxs-lookup"><span data-stu-id="acbe3-238">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
