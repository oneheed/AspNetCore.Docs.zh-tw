---
title: Razor ASP.NET Core 中的檔案編譯
author: rick-anderson
description: 瞭解如何 Razor 在 ASP.NET Core 應用程式中進行檔案編譯。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
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
ms.openlocfilehash: 3d76eff93d5c7c53b57136e5183e1ca5287dec81
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631117"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="29a8c-103">Razor ASP.NET Core 中的檔案編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="29a8c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29a8c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="29a8c-105">Razor具有*cshtml*副檔名的檔案會使用[ Razor SDK](xref:razor-pages/sdk)在組建和發行時間進行編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="29a8c-106">您可以藉由設定專案，選擇性地啟用執行時間編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="29a8c-107">Razor 編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-107">Razor compilation</span></span>

<span data-ttu-id="29a8c-108">RazorSDK 預設會啟用檔案的組建階段和發行時間編譯 Razor 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="29a8c-109">啟用時，執行時間編譯會補充組建時間編譯，讓檔案在編輯時可 Razor 進行更新。</span><span class="sxs-lookup"><span data-stu-id="29a8c-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="29a8c-110">在專案建立時啟用執行時間編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="29a8c-111">Razor頁面和 MVC 專案範本包含一個選項，可讓您在建立專案時啟用執行時間編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="29a8c-112">ASP.NET Core 3.1 和更新版本中支援此選項。</span><span class="sxs-lookup"><span data-stu-id="29a8c-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="29a8c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29a8c-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="29a8c-114">在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="29a8c-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="29a8c-115">選取 **Web 應用程式** 或 \*\*web 應用程式 (模型-視圖控制器) \*\* 專案範本。</span><span class="sxs-lookup"><span data-stu-id="29a8c-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="29a8c-116">選取 [ **啟用 Razor 執行時間編譯** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="29a8c-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="29a8c-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="29a8c-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="29a8c-118">使用 `-rrc` 或 `--razor-runtime-compilation` 範本選項。</span><span class="sxs-lookup"><span data-stu-id="29a8c-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="29a8c-119">例如，下列命令會建立 Razor 啟用執行時間編譯的新頁面專案：</span><span class="sxs-lookup"><span data-stu-id="29a8c-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="29a8c-120">在現有的專案中啟用執行時間編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="29a8c-121">若要針對現有專案中的所有環境啟用執行時間編譯：</span><span class="sxs-lookup"><span data-stu-id="29a8c-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="29a8c-122">請安裝 [AspNetCore Razor 。>microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="29a8c-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="29a8c-123">更新專案的 `Startup.ConfigureServices` 方法以包含的呼叫 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="29a8c-124">例如：</span><span class="sxs-lookup"><span data-stu-id="29a8c-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="29a8c-125">有條件地在現有的專案中啟用執行時間編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="29a8c-126">您可以啟用執行時間編譯，使其僅適用于本機開發。</span><span class="sxs-lookup"><span data-stu-id="29a8c-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="29a8c-127">有條件地以這種方式啟用，可確保已發佈的輸出：</span><span class="sxs-lookup"><span data-stu-id="29a8c-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="29a8c-128">使用編譯的視圖。</span><span class="sxs-lookup"><span data-stu-id="29a8c-128">Uses compiled views.</span></span>
* <span data-ttu-id="29a8c-129">不會在生產環境中啟用檔案監看員。</span><span class="sxs-lookup"><span data-stu-id="29a8c-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="29a8c-130">若只要啟用開發環境中的執行時間編譯：</span><span class="sxs-lookup"><span data-stu-id="29a8c-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="29a8c-131">請安裝 [AspNetCore Razor 。>microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="29a8c-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="29a8c-132">修改launchSettings.js中的啟動設定檔 `environmentVariables` 區段： *launchSettings.json*</span><span class="sxs-lookup"><span data-stu-id="29a8c-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="29a8c-133">確認 `ASPNETCORE_ENVIRONMENT` 設定為 `"Development"` 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="29a8c-134">將 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 設定為 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`。</span><span class="sxs-lookup"><span data-stu-id="29a8c-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="29a8c-135">在下列範例中，會在 `IIS Express` 和啟動設定檔的開發環境中啟用執行時間編譯 `RazorPagesApp` ：</span><span class="sxs-lookup"><span data-stu-id="29a8c-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="29a8c-136">專案類別中不需要變更程式碼 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="29a8c-137">在執行時間，ASP.NET Core 搜尋中的 [元件層級 HostingStartup 屬性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="29a8c-138">`HostingStartup`屬性會指定要執行的應用程式啟動程式碼。</span><span class="sxs-lookup"><span data-stu-id="29a8c-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="29a8c-139">該啟始程式碼會啟用執行時間編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="29a8c-140">啟用類別庫的執行時間編譯 Razor</span><span class="sxs-lookup"><span data-stu-id="29a8c-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="29a8c-141">假設有一個案例，其中的 Razor 頁面專案參考了[ Razor 類別庫 (](xref:razor-pages/ui-class)名為*MyClassLib*的 RCL) 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="29a8c-142">RCL 包含您所有小組的 MVC 和頁面專案都使用的 *_Layout cshtml* 檔案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="29a8c-143">您想要針對該 RCL 中的 *_Layout cshtml* 檔案啟用執行時間編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="29a8c-144">在頁面專案中進行下列變更 Razor ：</span><span class="sxs-lookup"><span data-stu-id="29a8c-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="29a8c-145">以有條件的指示啟用執行時間編譯，在 [現有的專案中啟用執行時間編譯](#conditionally-enable-runtime-compilation-in-an-existing-project)。</span><span class="sxs-lookup"><span data-stu-id="29a8c-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="29a8c-146">設定中的執行時間編譯選項 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="29a8c-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="29a8c-147">在上述程式碼中，會對 *MyClassLib* RCL 的絕對路徑進行結構化。</span><span class="sxs-lookup"><span data-stu-id="29a8c-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="29a8c-148">[PHYSICALFILEPROVIDER API](xref:fundamentals/file-providers#physicalfileprovider)是用來尋找位於該絕對路徑的目錄和檔案。</span><span class="sxs-lookup"><span data-stu-id="29a8c-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="29a8c-149">最後， `PhysicalFileProvider` 會將實例新增至檔案提供者集合，以允許存取 RCL 的 *cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="29a8c-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29a8c-150">其他資源</span><span class="sxs-lookup"><span data-stu-id="29a8c-150">Additional resources</span></span>

* <span data-ttu-id="29a8c-151">[ Razor CompileOnBuild 和 Razor CompileOnPublish](xref:razor-pages/sdk#properties)屬性。</span><span class="sxs-lookup"><span data-stu-id="29a8c-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="29a8c-152">Razor具有*cshtml*副檔名的檔案會使用[ Razor SDK](xref:razor-pages/sdk)在組建和發行時間進行編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="29a8c-153">您可以透過設定應用程式，選擇性地啟用執行階段編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="29a8c-154">Razor 編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-154">Razor compilation</span></span>

<span data-ttu-id="29a8c-155">RazorSDK 預設會啟用檔案的組建階段和發行時間編譯 Razor 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="29a8c-156">啟用時，執行時間編譯會補充組建時間編譯，讓檔案在編輯時可 Razor 進行更新。</span><span class="sxs-lookup"><span data-stu-id="29a8c-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="29a8c-157">執行階段編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-157">Runtime compilation</span></span>

<span data-ttu-id="29a8c-158">若要啟用所有環境和設定模式的執行時間編譯：</span><span class="sxs-lookup"><span data-stu-id="29a8c-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="29a8c-159">請安裝 [AspNetCore Razor 。>microsoft.aspnetcore.mvc.razor.runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="29a8c-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="29a8c-160">更新專案的 `Startup.ConfigureServices` 方法以包含的呼叫 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="29a8c-161">例如：</span><span class="sxs-lookup"><span data-stu-id="29a8c-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="29a8c-162">有條件地啟用執行時間編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="29a8c-163">您可以啟用執行時間編譯，使其僅適用于本機開發。</span><span class="sxs-lookup"><span data-stu-id="29a8c-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="29a8c-164">有條件地以這種方式啟用，可確保已發佈的輸出：</span><span class="sxs-lookup"><span data-stu-id="29a8c-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="29a8c-165">使用編譯的視圖。</span><span class="sxs-lookup"><span data-stu-id="29a8c-165">Uses compiled views.</span></span>
* <span data-ttu-id="29a8c-166">大小較小。</span><span class="sxs-lookup"><span data-stu-id="29a8c-166">Is smaller in size.</span></span>
* <span data-ttu-id="29a8c-167">不會在生產環境中啟用檔案監看員。</span><span class="sxs-lookup"><span data-stu-id="29a8c-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="29a8c-168">若要根據環境和設定模式啟用執行時間編譯：</span><span class="sxs-lookup"><span data-stu-id="29a8c-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="29a8c-169">有條件地參考 [AspNetCore Razor 。](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 以作用中的值為基礎的 >microsoft.aspnetcore.mvc.razor.runtimecompilation 套件 `Configuration` ：</span><span class="sxs-lookup"><span data-stu-id="29a8c-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="29a8c-170">更新專案的 `Startup.ConfigureServices` 方法以包含的呼叫 `AddRazorRuntimeCompilation` 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="29a8c-171">有條件 `AddRazorRuntimeCompilation` 地執行，如此 `ASPNETCORE_ENVIRONMENT` 一來，當變數設定為時，它才會在 Debug 模式中執行 `Development` ：</span><span class="sxs-lookup"><span data-stu-id="29a8c-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="29a8c-172">其他資源</span><span class="sxs-lookup"><span data-stu-id="29a8c-172">Additional resources</span></span>

* <span data-ttu-id="29a8c-173">[ Razor CompileOnBuild 和 Razor CompileOnPublish](xref:razor-pages/sdk#properties)屬性。</span><span class="sxs-lookup"><span data-stu-id="29a8c-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="29a8c-174">請參閱 [GitHub 上的執行時間編譯範例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) ，以取得可讓執行時間編譯跨專案運作的範例。</span><span class="sxs-lookup"><span data-stu-id="29a8c-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29a8c-175">當叫用 Razor 相關聯的 Razor 頁面或 MVC 視圖時，會在執行時間編譯檔案。</span><span class="sxs-lookup"><span data-stu-id="29a8c-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="29a8c-176">Razor檔案會使用[ Razor SDK](xref:razor-pages/sdk)在組建和發行時間進行編譯。</span><span class="sxs-lookup"><span data-stu-id="29a8c-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="29a8c-177">Razor 編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-177">Razor compilation</span></span>

<span data-ttu-id="29a8c-178">RazorSDK 預設會啟用檔案的組建和發行時間編譯 Razor 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="29a8c-179">Razor在檔案更新後編輯檔案，會在組建階段受到支援。</span><span class="sxs-lookup"><span data-stu-id="29a8c-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="29a8c-180">依預設，只有編譯的 *Views.dll* 以及編譯檔案時所需的任何 *cshtml* 檔案或參考元件 Razor 會隨您的應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="29a8c-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="29a8c-181">先行編譯工具已被取代，且將在 ASP.NET Core 3.0 中移除。</span><span class="sxs-lookup"><span data-stu-id="29a8c-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="29a8c-182">建議您遷移至[ Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="29a8c-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="29a8c-183">Razor只有在專案檔中未設定任何先行編譯特定屬性時，SDK 才會生效。</span><span class="sxs-lookup"><span data-stu-id="29a8c-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="29a8c-184">例如，將 *.csproj* 檔案的 `MvcRazorCompileOnPublish` 屬性設定為停用 `true` Razor SDK。</span><span class="sxs-lookup"><span data-stu-id="29a8c-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="29a8c-185">執行階段編譯</span><span class="sxs-lookup"><span data-stu-id="29a8c-185">Runtime compilation</span></span>

<span data-ttu-id="29a8c-186">組建階段編譯是由檔案的執行時間編譯所補充 Razor 。</span><span class="sxs-lookup"><span data-stu-id="29a8c-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="29a8c-187">Razor當*cshtml*檔案的內容變更時，ASP.NET Core MVC 會重新編譯檔案。</span><span class="sxs-lookup"><span data-stu-id="29a8c-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29a8c-188">其他資源</span><span class="sxs-lookup"><span data-stu-id="29a8c-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
