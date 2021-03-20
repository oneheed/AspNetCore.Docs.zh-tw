---
title: 在類別庫中使用 ASP.NET Core Api
author: scottaddie
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
ms.openlocfilehash: 454f2523a44f5c1aa12b9a27c21c6a3e933ab81a
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711475"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="20529-103">在類別庫中使用 ASP.NET Core Api</span><span class="sxs-lookup"><span data-stu-id="20529-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="20529-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="20529-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="20529-105">本檔提供在類別庫中使用 ASP.NET Core Api 的指引。</span><span class="sxs-lookup"><span data-stu-id="20529-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="20529-106">如需其他程式庫的指引，請參閱 [開放原始碼程式庫指引](/dotnet/standard/library-guidance/)。</span><span class="sxs-lookup"><span data-stu-id="20529-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="20529-107">判斷要支援哪些 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="20529-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="20529-108">ASP.NET Core 遵守 [.Net Core 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="20529-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="20529-109">判斷要在程式庫中支援哪些 ASP.NET Core 版本時，請參閱支援原則。</span><span class="sxs-lookup"><span data-stu-id="20529-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="20529-110">程式庫應：</span><span class="sxs-lookup"><span data-stu-id="20529-110">A library should:</span></span>

* <span data-ttu-id="20529-111">請努力支援所有分類為 *長期支援* (LTS) 的 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="20529-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="20529-112">不會受到支援 ASP.NET Core 版本分類為) *的生命週期結束* (。</span><span class="sxs-lookup"><span data-stu-id="20529-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="20529-113">當 ASP.NET Core 的預覽版本可供使用時，會將重大變更張貼在 [aspnet/公告](https://github.com/aspnet/Announcements/issues) GitHub 存放庫中。</span><span class="sxs-lookup"><span data-stu-id="20529-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="20529-114">您可以在開發架構功能時進行程式庫的相容性測試。</span><span class="sxs-lookup"><span data-stu-id="20529-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="20529-115">使用 ASP.NET Core 共用架構</span><span class="sxs-lookup"><span data-stu-id="20529-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="20529-116">隨著 .NET Core 3.0 的發行，許多 ASP.NET Core 元件不再以套件的形式發佈至 NuGet。</span><span class="sxs-lookup"><span data-stu-id="20529-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="20529-117">相反地，這些元件會包含在 `Microsoft.AspNetCore.App` 與 .NET Core SDK 和執行時間安裝程式一起安裝的共用架構中。</span><span class="sxs-lookup"><span data-stu-id="20529-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="20529-118">如需不再發佈的套件清單，請參閱 [移除淘汰的封裝參考](xref:migration/22-to-30#remove-obsolete-package-references)。</span><span class="sxs-lookup"><span data-stu-id="20529-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="20529-119">從 .NET Core 3.0，使用 MSBuild SDK 的專案會 `Microsoft.NET.Sdk.Web` 隱含地參考共用架構。</span><span class="sxs-lookup"><span data-stu-id="20529-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="20529-120">使用 `Microsoft.NET.Sdk` 或 SDK 的專案 `Microsoft.NET.Sdk.Razor` 必須參考 ASP.NET Core，才能在共用架構中使用 ASP.NET Core api。</span><span class="sxs-lookup"><span data-stu-id="20529-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="20529-121">若要參考 ASP.NET Core，請將下列 `<FrameworkReference>` 元素新增至您的專案檔：</span><span class="sxs-lookup"><span data-stu-id="20529-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="20529-122">以這種方式參考 ASP.NET Core 僅支援以 .NET Core 3.x 為目標的專案。</span><span class="sxs-lookup"><span data-stu-id="20529-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="20529-123">包含 Blazor 擴充性</span><span class="sxs-lookup"><span data-stu-id="20529-123">Include Blazor extensibility</span></span>

<span data-ttu-id="20529-124">Blazor 支援 WebAssembly (WASM) 和以伺服器為基礎的 [裝載模型](xref:blazor/hosting-models)。</span><span class="sxs-lookup"><span data-stu-id="20529-124">Blazor supports WebAssembly (WASM) and server-based [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="20529-125">除非有特定原因不支援這兩個裝載模型，否則[ Razor 元件](xref:blazor/components/index)程式庫應同時支援這兩種裝載模型。</span><span class="sxs-lookup"><span data-stu-id="20529-125">Unless there's a specific reason not to support both hosting models, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="20529-126">Razor元件程式庫必須使用[Microsoft .Net. Sdk Razor 。SDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="20529-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="20529-127">支援兩種裝載模型</span><span class="sxs-lookup"><span data-stu-id="20529-127">Support both hosting models</span></span>

<span data-ttu-id="20529-128">若要支援 Razor 和專案的元件耗用量 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) [Blazor Server](xref:blazor/hosting-models#blazor-server) ，請針對您的編輯器使用下列指示。</span><span class="sxs-lookup"><span data-stu-id="20529-128">To support Razor component consumption by [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) and [Blazor Server](xref:blazor/hosting-models#blazor-server) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20529-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20529-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20529-130">使用 [ **Razor 類別庫**] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="20529-130">Use the **Razor Class Library** project template.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20529-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20529-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="20529-132">在整合式終端機中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="20529-132">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20529-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20529-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20529-134">使用 [ **Razor 類別庫**] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="20529-134">Use the **Razor Class Library** project template.</span></span>

---

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="20529-135">從專案範本產生的程式庫：</span><span class="sxs-lookup"><span data-stu-id="20529-135">The library generated from the project template:</span></span>

* <span data-ttu-id="20529-136">根據已安裝的 SDK，以目前的 .NET framework 為目標。</span><span class="sxs-lookup"><span data-stu-id="20529-136">Targets the current .NET framework based on the installed SDK.</span></span>
* <span data-ttu-id="20529-137">藉由包含 MSBuild 專案所支援的平臺，讓瀏覽器相容性檢查平臺相依性 `browser` `SupportedPlatform` 。</span><span class="sxs-lookup"><span data-stu-id="20529-137">Enables browser compatibilty checks for platform dependencies by including `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span>
* <span data-ttu-id="20529-138">新增 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)的 NuGet 套件參考。</span><span class="sxs-lookup"><span data-stu-id="20529-138">Adds a NuGet package reference for [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web).</span></span>

<span data-ttu-id="20529-139">範例：</span><span class="sxs-lookup"><span data-stu-id="20529-139">Example:</span></span>

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

<span data-ttu-id="20529-140">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="20529-140">In the preceding example:</span></span>

* <span data-ttu-id="20529-141">`{TARGET FRAMEWORK}`預留位置是[TFM)  (的目標 Framework 標記](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="20529-141">The `{TARGET FRAMEWORK}` placeholder is the [Target Framework Moniker (TFM)](/dotnet/standard/frameworks).</span></span>
* <span data-ttu-id="20529-142">`{VERSION}`預留位置是封裝的版本 [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 。</span><span class="sxs-lookup"><span data-stu-id="20529-142">The `{VERSION}` placeholder is the version of the [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) package.</span></span>

### <a name="only-support-the-blazor-server-hosting-model"></a><span data-ttu-id="20529-143">僅支援 Blazor Server 裝載模型</span><span class="sxs-lookup"><span data-stu-id="20529-143">Only support the Blazor Server hosting model</span></span>

<span data-ttu-id="20529-144">類別庫很少只支援 [Blazor Server](xref:blazor/hosting-models#blazor-server) 應用程式。</span><span class="sxs-lookup"><span data-stu-id="20529-144">Class libraries rarely only support [Blazor Server](xref:blazor/hosting-models#blazor-server) apps.</span></span> <span data-ttu-id="20529-145">如果類別庫需要 [Blazor Server](xref:blazor/hosting-models#blazor-server) 特定的功能，例如或的存取， <xref:Microsoft.AspNetCore.Components.Server.Circuits.CircuitHandler> <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage> 或使用 ASP.NET Core 特定的功能，例如中介軟體、MVC 控制器或 Razor 頁面，請使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="20529-145">If the class library requires [Blazor Server](xref:blazor/hosting-models#blazor-server)-specific features, such as access to <xref:Microsoft.AspNetCore.Components.Server.Circuits.CircuitHandler>s or <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage>, or uses ASP.NET Core-specific features, such as middleware, MVC controllers, or Razor Pages, use **one** of the following approaches:</span></span>

* <span data-ttu-id="20529-146">指定當使用 **支援頁面和 views** 核取方塊 (Visual Studio) 或使用命令的選項來建立專案時，程式庫支援頁面和流覽 `-s|--support-pages-and-views` `dotnet new` ：</span><span class="sxs-lookup"><span data-stu-id="20529-146">Specify that the library supports pages and views when the project is created with the **Support pages and views** check box (Visual Studio) or the `-s|--support-pages-and-views` option with the `dotnet new` command:</span></span>

  ```dotnetcli
  dotnet new razorclasslib -s true
  ```

* <span data-ttu-id="20529-147">只在程式庫的專案檔中提供架構參考給 ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="20529-147">Only provide a framework reference to ASP.NET Core in the library's project file:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Razor">

    <ItemGroup>
      <FrameworkReference Include="Microsoft.AspNetCore.App" />
    </ItemGroup>

  </Project>
  ```

### <a name="support-multiple-framework-versions"></a><span data-ttu-id="20529-148">支援多個架構版本</span><span class="sxs-lookup"><span data-stu-id="20529-148">Support multiple framework versions</span></span>

<span data-ttu-id="20529-149">如果程式庫必須支援 Blazor 在目前版本中新增至的功能，同時也支援一或多個較早的版本，請多目標程式庫。</span><span class="sxs-lookup"><span data-stu-id="20529-149">If the library must support features added to Blazor in the current release while also supporting one or more earlier releases, multi-target the library.</span></span> <span data-ttu-id="20529-150">在 MSBuild 屬性中提供以分號分隔的 [目標 Framework 標記 (tfm) ](/dotnet/standard/frameworks) 清單 `TargetFrameworks` ：</span><span class="sxs-lookup"><span data-stu-id="20529-150">Provide a semicolon-separated list of [Target Framework Monikers (TFMs)](/dotnet/standard/frameworks) in the `TargetFrameworks` MSBuild property:</span></span>

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

<span data-ttu-id="20529-151">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="20529-151">In the preceding example:</span></span>

* <span data-ttu-id="20529-152">`{TARGET FRAMEWORKS}`預留位置代表以分號分隔的 tfm 清單。</span><span class="sxs-lookup"><span data-stu-id="20529-152">The `{TARGET FRAMEWORKS}` placeholder represents the semicolon-separated TFMs list.</span></span> <span data-ttu-id="20529-153">例如： `netcoreapp3.1;net5.0` 。</span><span class="sxs-lookup"><span data-stu-id="20529-153">For example, `netcoreapp3.1;net5.0`.</span></span>
* <span data-ttu-id="20529-154">`{VERSION}`預留位置是封裝的版本 [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 。</span><span class="sxs-lookup"><span data-stu-id="20529-154">The `{VERSION}` placeholder is the version of the [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="20529-155">從範本產生的專案：</span><span class="sxs-lookup"><span data-stu-id="20529-155">The project generated from the template:</span></span>

* <span data-ttu-id="20529-156">目標 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="20529-156">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="20529-157">將 `RazorLangVersion` 屬性設定為 `3.0`。</span><span class="sxs-lookup"><span data-stu-id="20529-157">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="20529-158">`3.0` 是 .NET Core 3.x 的預設值。</span><span class="sxs-lookup"><span data-stu-id="20529-158">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="20529-159">新增下列封裝參考：</span><span class="sxs-lookup"><span data-stu-id="20529-159">Adds the following package references:</span></span>
  * [<span data-ttu-id="20529-160">AspNetCore 元件</span><span class="sxs-lookup"><span data-stu-id="20529-160">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="20529-161">AspNetCore Web 元件</span><span class="sxs-lookup"><span data-stu-id="20529-161">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="20529-162">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-162">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="20529-163">支援特定的裝載模型</span><span class="sxs-lookup"><span data-stu-id="20529-163">Support a specific hosting model</span></span>

<span data-ttu-id="20529-164">最常見的情況是支援單一 Blazor 裝載模型。</span><span class="sxs-lookup"><span data-stu-id="20529-164">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="20529-165">舉例而言， Razor 只支援專案的元件耗用量 [Blazor Server](xref:blazor/hosting-models#blazor-server) ：</span><span class="sxs-lookup"><span data-stu-id="20529-165">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="20529-166">以 .NET Core 2.x 為目標。</span><span class="sxs-lookup"><span data-stu-id="20529-166">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="20529-167">新增 `<FrameworkReference>` 共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-167">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="20529-168">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-168">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

::: moniker-end

<span data-ttu-id="20529-169">如需包含元件之程式庫的詳細資訊 Razor ，請參閱 <xref:blazor/components/class-libraries> 。</span><span class="sxs-lookup"><span data-stu-id="20529-169">For more information on libraries containing Razor components, see <xref:blazor/components/class-libraries>.</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="20529-170">包含 MVC 擴充性</span><span class="sxs-lookup"><span data-stu-id="20529-170">Include MVC extensibility</span></span>

<span data-ttu-id="20529-171">本節概述程式庫的建議，包括：</span><span class="sxs-lookup"><span data-stu-id="20529-171">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="20529-172">Razor views 或 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="20529-172">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="20529-173">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="20529-173">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="20529-174">檢視元件</span><span class="sxs-lookup"><span data-stu-id="20529-174">View components</span></span>](#view-components)

<span data-ttu-id="20529-175">本節不會討論多目標，以支援多個版本的 MVC。</span><span class="sxs-lookup"><span data-stu-id="20529-175">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="20529-176">如需支援多個 ASP.NET Core 版本的指引，請參閱 [支援多個 ASP.NET Core 版本](#support-multiple-aspnet-core-versions)。</span><span class="sxs-lookup"><span data-stu-id="20529-176">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="20529-177">Razor views 或 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="20529-177">Razor views or Razor Pages</span></span>

<span data-ttu-id="20529-178">包含[ Razor 視圖](xref:mvc/views/overview)或[ Razor 頁面](xref:razor-pages/index)的專案必須使用[Microsoft .net Sdk Razor 。SDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="20529-178">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="20529-179">如果專案是以 .NET Core 2.x 為目標，則需要：</span><span class="sxs-lookup"><span data-stu-id="20529-179">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="20529-180">`AddRazorSupportForMvc`設定為的 MSBuild 屬性 `true` 。</span><span class="sxs-lookup"><span data-stu-id="20529-180">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="20529-181">`<FrameworkReference>`共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-181">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="20529-182">**Razor 類別庫** 專案範本滿足上述以 .net Core 2.x 為目標的專案需求。</span><span class="sxs-lookup"><span data-stu-id="20529-182">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="20529-183">針對您的編輯器，請使用下列指示。</span><span class="sxs-lookup"><span data-stu-id="20529-183">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20529-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20529-184">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20529-185">使用 [ **Razor 類別庫**] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="20529-185">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="20529-186">應選取範本的 [ **支援頁面和流覽** 器] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="20529-186">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20529-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20529-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="20529-188">在整合式終端機中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="20529-188">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20529-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20529-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20529-190">目前沒有任何專案範本支援。</span><span class="sxs-lookup"><span data-stu-id="20529-190">No project template support at this time.</span></span>

---

<span data-ttu-id="20529-191">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-191">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="20529-192">如果專案是以 .NET Standard 為目標，則需要 [AspNetCore 的 Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) 封裝參考。</span><span class="sxs-lookup"><span data-stu-id="20529-192">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="20529-193">`Microsoft.AspNetCore.Mvc`封裝會移至 ASP.NET Core 3.0 的共用架構中，因此不會再發佈。</span><span class="sxs-lookup"><span data-stu-id="20529-193">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="20529-194">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-194">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="20529-195">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="20529-195">Tag Helpers</span></span>

<span data-ttu-id="20529-196">包含 [標記](xref:mvc/views/tag-helpers/intro) 協助程式的專案應該使用 `Microsoft.NET.Sdk` SDK。</span><span class="sxs-lookup"><span data-stu-id="20529-196">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="20529-197">如果以 .NET Core 3.x 為目標，請新增 `<FrameworkReference>` 共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-197">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="20529-198">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-198">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="20529-199">如果目標 .NET Standard (支援早于 ASP.NET Core 3.x) 的版本，請將套件參考新增至[AspNetCore Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)。</span><span class="sxs-lookup"><span data-stu-id="20529-199">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="20529-200">`Microsoft.AspNetCore.Mvc.Razor`封裝會移至共用架構中，因此不會再發佈。</span><span class="sxs-lookup"><span data-stu-id="20529-200">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="20529-201">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-201">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="20529-202">檢視元件</span><span class="sxs-lookup"><span data-stu-id="20529-202">View components</span></span>

<span data-ttu-id="20529-203">包含 [View 元件](xref:mvc/views/view-components) 的專案應該使用 `Microsoft.NET.Sdk` SDK。</span><span class="sxs-lookup"><span data-stu-id="20529-203">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="20529-204">如果以 .NET Core 3.x 為目標，請新增 `<FrameworkReference>` 共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-204">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="20529-205">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-205">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="20529-206">如果目標 .NET Standard (支援早于 ASP.NET Core 3.x) 的版本，請將套件參考新增至 [ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)。</span><span class="sxs-lookup"><span data-stu-id="20529-206">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="20529-207">`Microsoft.AspNetCore.Mvc.ViewFeatures`封裝會移至共用架構中，因此不會再發佈。</span><span class="sxs-lookup"><span data-stu-id="20529-207">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="20529-208">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-208">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="20529-209">支援多個 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="20529-209">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="20529-210">需要多目標才能撰寫支援多種 ASP.NET Core 變體的程式庫。</span><span class="sxs-lookup"><span data-stu-id="20529-210">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="20529-211">請考慮標籤協助程式程式庫必須支援下列 ASP.NET Core 變體的案例：</span><span class="sxs-lookup"><span data-stu-id="20529-211">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="20529-212">ASP.NET Core 2.1 的目標 .NET Framework 4.6。1</span><span class="sxs-lookup"><span data-stu-id="20529-212">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="20529-213">以 .NET Core 2.x 為目標的 ASP.NET Core 2。x</span><span class="sxs-lookup"><span data-stu-id="20529-213">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="20529-214">以 .NET Core 2.x 為目標的 ASP.NET Core 3。x</span><span class="sxs-lookup"><span data-stu-id="20529-214">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="20529-215">下列專案檔透過屬性支援這些變數 `TargetFrameworks` ：</span><span class="sxs-lookup"><span data-stu-id="20529-215">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="20529-216">使用上述的專案檔：</span><span class="sxs-lookup"><span data-stu-id="20529-216">With the preceding project file:</span></span>

* <span data-ttu-id="20529-217">`Markdig`針對所有取用者新增套件。</span><span class="sxs-lookup"><span data-stu-id="20529-217">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="20529-218">AspNetCore 的參考[。 Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span><span class="sxs-lookup"><span data-stu-id="20529-218">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="20529-219">是針對以 .NET Framework 4.6.1 或更新版本或 .NET Core 2.x 為目標的取用者新增的。</span><span class="sxs-lookup"><span data-stu-id="20529-219">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="20529-220">因為回溯相容性的原因，套件的版本2.1.0 適用于 ASP.NET Core 2.2。</span><span class="sxs-lookup"><span data-stu-id="20529-220">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="20529-221">針對以 .NET Core 2.x 為目標的取用者，會參考共用架構。</span><span class="sxs-lookup"><span data-stu-id="20529-221">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="20529-222">此 `Microsoft.AspNetCore.Mvc.Razor` 封裝包含在共用架構中。</span><span class="sxs-lookup"><span data-stu-id="20529-222">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="20529-223">或者，您可以將 .NET Standard 2.0 的目標設為目標，而不是以 .NET Core 2.1 和 .NET Framework 4.6.1 為目標：</span><span class="sxs-lookup"><span data-stu-id="20529-223">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="20529-224">使用上述的專案檔時，有下列注意事項：</span><span class="sxs-lookup"><span data-stu-id="20529-224">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="20529-225">因為此程式庫只包含標籤協助程式，所以以 ASP.NET Core 執行所在的特定平臺為目標更為直接： .NET Core 和 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="20529-225">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="20529-226">標籤協助程式無法供其他 .NET Standard 符合2.0 規範的目標架構（例如 Unity、UWP 和 Xamarin）使用。</span><span class="sxs-lookup"><span data-stu-id="20529-226">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="20529-227">使用 .NET Framework 中的 .NET Standard 2.0，會有一些在 .NET Framework 4.7.2 中已經解決的問題。</span><span class="sxs-lookup"><span data-stu-id="20529-227">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="20529-228">您可以將目標設為 .NET Framework 4.6.1，藉以改善使用 .NET Framework 4.6.1 至4.7.1 的取用者體驗。</span><span class="sxs-lookup"><span data-stu-id="20529-228">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="20529-229">如果您的程式庫需要呼叫平臺特定的 Api，請以特定的 .NET 實作為目標，而不是 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="20529-229">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="20529-230">如需詳細資訊，請參閱 [多目標](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)。</span><span class="sxs-lookup"><span data-stu-id="20529-230">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="20529-231">使用未變更的 API</span><span class="sxs-lookup"><span data-stu-id="20529-231">Use an API that hasn't changed</span></span>

<span data-ttu-id="20529-232">想像您要將中介軟體程式庫從 .NET Core 2.2 升級到3.0 的案例。</span><span class="sxs-lookup"><span data-stu-id="20529-232">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="20529-233">程式庫中使用的 ASP.NET Core 中介軟體 Api 未在 ASP.NET Core 2.2 和3.0 之間變更。</span><span class="sxs-lookup"><span data-stu-id="20529-233">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="20529-234">若要繼續支援 .NET Core 3.0 中的中介軟體程式庫，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="20529-234">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="20529-235">遵循 [標準程式庫指引](/dotnet/standard/library-guidance/)。</span><span class="sxs-lookup"><span data-stu-id="20529-235">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="20529-236">如果對應的元件不存在於共用架構中，請為每個 API 的 NuGet 套件新增套件參考。</span><span class="sxs-lookup"><span data-stu-id="20529-236">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="20529-237">使用變更的 API</span><span class="sxs-lookup"><span data-stu-id="20529-237">Use an API that changed</span></span>

<span data-ttu-id="20529-238">想像您要將程式庫從 .NET Core 2.2 升級至 .NET Core 3.0 的案例。</span><span class="sxs-lookup"><span data-stu-id="20529-238">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="20529-239">程式庫中使用的 ASP.NET Core API 在 ASP.NET Core 3.0 中有 [重大變更](/dotnet/core/compatibility/breaking-changes) 。</span><span class="sxs-lookup"><span data-stu-id="20529-239">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="20529-240">請考慮是否可以重寫程式庫，而不在所有版本中使用中斷的 API。</span><span class="sxs-lookup"><span data-stu-id="20529-240">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="20529-241">如果您可以重寫程式庫，請執行此動作，並繼續以較早的目標 framework 為目標 (例如 .NET Standard 2.0 或使用套件參考 .NET Framework 4.6.1) 。</span><span class="sxs-lookup"><span data-stu-id="20529-241">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="20529-242">如果您無法重寫程式庫，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="20529-242">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="20529-243">新增 .NET Core 3.0 的目標。</span><span class="sxs-lookup"><span data-stu-id="20529-243">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="20529-244">新增 `<FrameworkReference>` 共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-244">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="20529-245">使用 [#if 預處理器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) 指示詞搭配適當的目標 framework 符號，有條件地編譯器代碼。</span><span class="sxs-lookup"><span data-stu-id="20529-245">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="20529-246">例如，HTTP 要求和回應資料流程的同步讀取和寫入預設會在 ASP.NET Core 3.0 時停用。</span><span class="sxs-lookup"><span data-stu-id="20529-246">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="20529-247">ASP.NET Core 2.2 預設支援同步行為。</span><span class="sxs-lookup"><span data-stu-id="20529-247">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="20529-248">假設有一個中介軟體程式庫，應該在發生 i/o 的位置啟用同步讀取和寫入。</span><span class="sxs-lookup"><span data-stu-id="20529-248">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="20529-249">程式庫應該在適當的預處理器指示詞中包含程式碼，以啟用同步功能。</span><span class="sxs-lookup"><span data-stu-id="20529-249">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="20529-250">例如：</span><span class="sxs-lookup"><span data-stu-id="20529-250">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="20529-251">使用3.0 中引進的 API</span><span class="sxs-lookup"><span data-stu-id="20529-251">Use an API introduced in 3.0</span></span>

<span data-ttu-id="20529-252">假設您想要使用 ASP.NET Core 3.0 中引進的 ASP.NET Core API。</span><span class="sxs-lookup"><span data-stu-id="20529-252">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="20529-253">請考量下列問題：</span><span class="sxs-lookup"><span data-stu-id="20529-253">Consider the following questions:</span></span>

1. <span data-ttu-id="20529-254">程式庫功能是否需要新的 API？</span><span class="sxs-lookup"><span data-stu-id="20529-254">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="20529-255">程式庫可以用不同的方式來執行這項功能嗎？</span><span class="sxs-lookup"><span data-stu-id="20529-255">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="20529-256">如果媒體櫃的功能需要 API，而且沒有任何方法可將它向下層級執行：</span><span class="sxs-lookup"><span data-stu-id="20529-256">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="20529-257">僅限以 .NET Core 2.x 為目標。</span><span class="sxs-lookup"><span data-stu-id="20529-257">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="20529-258">新增 `<FrameworkReference>` 共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-258">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="20529-259">如果程式庫可以使用不同的方式來執行此功能：</span><span class="sxs-lookup"><span data-stu-id="20529-259">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="20529-260">新增 .NET Core 3.x 作為目標 framework。</span><span class="sxs-lookup"><span data-stu-id="20529-260">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="20529-261">新增 `<FrameworkReference>` 共用架構的元素。</span><span class="sxs-lookup"><span data-stu-id="20529-261">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="20529-262">使用 [#if 預處理器](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) 指示詞搭配適當的目標 framework 符號，有條件地編譯器代碼。</span><span class="sxs-lookup"><span data-stu-id="20529-262">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="20529-263">例如，下列標記協助程式會使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> ASP.NET Core 3.0 中引進的介面。</span><span class="sxs-lookup"><span data-stu-id="20529-263">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="20529-264">以 .NET Core 3.0 為目標的取用者會執行目標 framework 符號所定義的程式碼路徑 `NETCOREAPP3_0` 。</span><span class="sxs-lookup"><span data-stu-id="20529-264">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="20529-265">標籤協助程式的函式參數類型會 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 針對 .Net Core 2.1 和 .NET Framework 4.6.1 取用者變更為。</span><span class="sxs-lookup"><span data-stu-id="20529-265">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="20529-266">這是必要的變更，因為 ASP.NET Core 3.0 標示 `IHostingEnvironment` 為已淘汰，建議 `IWebHostEnvironment` 取代。</span><span class="sxs-lookup"><span data-stu-id="20529-266">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

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

<span data-ttu-id="20529-267">下列多目標專案檔支援此標籤協助程式案例：</span><span class="sxs-lookup"><span data-stu-id="20529-267">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="20529-268">使用從共用架構移除的 API</span><span class="sxs-lookup"><span data-stu-id="20529-268">Use an API removed from the shared framework</span></span>

<span data-ttu-id="20529-269">若要使用從共用架構中移除的 ASP.NET Core 元件，請新增適當的封裝參考。</span><span class="sxs-lookup"><span data-stu-id="20529-269">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="20529-270">如需 ASP.NET Core 3.0 中從共用架構移除的封裝清單，請參閱 [移除淘汰的封裝參考](xref:migration/22-to-30#remove-obsolete-package-references)。</span><span class="sxs-lookup"><span data-stu-id="20529-270">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="20529-271">例如，若要新增 web API 用戶端：</span><span class="sxs-lookup"><span data-stu-id="20529-271">For example, to add the web API client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="20529-272">其他資源</span><span class="sxs-lookup"><span data-stu-id="20529-272">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="20529-273">.NET 實作支援</span><span class="sxs-lookup"><span data-stu-id="20529-273">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="20529-274">.NET 支援原則</span><span class="sxs-lookup"><span data-stu-id="20529-274">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
