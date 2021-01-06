---
title: dotnet aspnet-codegenerator 命令
author: rick-anderson
description: dotnet aspnet-codegenerator 命令會架起 ASP.NET Core 專案。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920699"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="724ac-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="724ac-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="724ac-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="724ac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="724ac-105">`dotnet aspnet-codegenerator` - 執行 ASP.NET Core Scaffolding 引擎。</span><span class="sxs-lookup"><span data-stu-id="724ac-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="724ac-106">從命令列進行 Scaffolding 時才需要 `dotnet aspnet-codegenerator`，在 Visual Studio 中不需要進行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="724ac-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="724ac-107">安裝及更新 aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="724ac-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="724ac-108">安裝 [.NET SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="724ac-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="724ac-109">`dotnet-aspnet-codegenerator` 是必須安裝的[全域工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="724ac-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="724ac-110">下列命令會安裝 `dotnet-aspnet-codegenerator` 工具的最新穩定版本：</span><span class="sxs-lookup"><span data-stu-id="724ac-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="724ac-111">下列命令會將 `dotnet-aspnet-codegenerator` 更新到可從已安裝之 .NET Core SDK 中取得的最新穩定版本：</span><span class="sxs-lookup"><span data-stu-id="724ac-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="724ac-112">卸載 aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="724ac-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="724ac-113">可能需要卸載 `aspnet-codegenerator` 才能解決問題。</span><span class="sxs-lookup"><span data-stu-id="724ac-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="724ac-114">例如，如果您已安裝的預覽版本 `aspnet-codegenerator` ，請先將其卸載，再安裝發行版本。</span><span class="sxs-lookup"><span data-stu-id="724ac-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="724ac-115">下列命令會卸載 `dotnet-aspnet-codegenerator` 工具並安裝最新穩定版本：</span><span class="sxs-lookup"><span data-stu-id="724ac-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="724ac-116">概要</span><span class="sxs-lookup"><span data-stu-id="724ac-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="724ac-117">描述</span><span class="sxs-lookup"><span data-stu-id="724ac-117">Description</span></span>

<span data-ttu-id="724ac-118">`dotnet aspnet-codegenerator` 全域命令會執行 ASP.NET Core 程式碼產生器與 Scaffolding 引擎。</span><span class="sxs-lookup"><span data-stu-id="724ac-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="724ac-119">引數</span><span class="sxs-lookup"><span data-stu-id="724ac-119">Arguments</span></span>

`generator`

<span data-ttu-id="724ac-120">要執行的程式碼產生器。</span><span class="sxs-lookup"><span data-stu-id="724ac-120">The code generator to run.</span></span> <span data-ttu-id="724ac-121">可用產生器如下︰</span><span class="sxs-lookup"><span data-stu-id="724ac-121">The following generators are available:</span></span>

| <span data-ttu-id="724ac-122">Generator</span><span class="sxs-lookup"><span data-stu-id="724ac-122">Generator</span></span>  | <span data-ttu-id="724ac-123">作業</span><span class="sxs-lookup"><span data-stu-id="724ac-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="724ac-124">區域</span><span class="sxs-lookup"><span data-stu-id="724ac-124">area</span></span>       | [<span data-ttu-id="724ac-125">架起區域</span><span class="sxs-lookup"><span data-stu-id="724ac-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="724ac-126">controller</span><span class="sxs-lookup"><span data-stu-id="724ac-126">controller</span></span> | [<span data-ttu-id="724ac-127">架起控制器</span><span class="sxs-lookup"><span data-stu-id="724ac-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="724ac-128">身分識別</span><span class="sxs-lookup"><span data-stu-id="724ac-128">identity</span></span>   | [<span data-ttu-id="724ac-129">支架 Identity</span><span class="sxs-lookup"><span data-stu-id="724ac-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="724ac-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="724ac-130">razorpage</span></span>  | [<span data-ttu-id="724ac-131">Scaffold Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="724ac-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="724ac-132">檢視</span><span class="sxs-lookup"><span data-stu-id="724ac-132">view</span></span>       | [<span data-ttu-id="724ac-133">架起檢視</span><span class="sxs-lookup"><span data-stu-id="724ac-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="724ac-134">選項</span><span class="sxs-lookup"><span data-stu-id="724ac-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="724ac-135">指定 NuGet 套件目錄。</span><span class="sxs-lookup"><span data-stu-id="724ac-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="724ac-136">定義組建組態。</span><span class="sxs-lookup"><span data-stu-id="724ac-136">Defines the build configuration.</span></span> <span data-ttu-id="724ac-137">預設值是 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="724ac-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="724ac-138">要使用的目標 [Framework](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="724ac-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="724ac-139">例如： `net46` 。</span><span class="sxs-lookup"><span data-stu-id="724ac-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="724ac-140">建置基底路徑。</span><span class="sxs-lookup"><span data-stu-id="724ac-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="724ac-141">印出命令的簡短說明。</span><span class="sxs-lookup"><span data-stu-id="724ac-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="724ac-142">不會在執行前建置專案。</span><span class="sxs-lookup"><span data-stu-id="724ac-142">Doesn't build the project before running.</span></span> <span data-ttu-id="724ac-143">選項也會隱含設定 `--no-restore` 旗標。</span><span class="sxs-lookup"><span data-stu-id="724ac-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="724ac-144">指定要執行的專案檔路徑 (資料夾名稱或完整路徑)。</span><span class="sxs-lookup"><span data-stu-id="724ac-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="724ac-145">如果未指定，則會預設為目前目錄。</span><span class="sxs-lookup"><span data-stu-id="724ac-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="724ac-146">產生器選項</span><span class="sxs-lookup"><span data-stu-id="724ac-146">Generator options</span></span>

<span data-ttu-id="724ac-147">以下各節詳細說明支援之產生器的可用選項：</span><span class="sxs-lookup"><span data-stu-id="724ac-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="724ac-148">區域</span><span class="sxs-lookup"><span data-stu-id="724ac-148">Area</span></span>
* <span data-ttu-id="724ac-149">控制器</span><span class="sxs-lookup"><span data-stu-id="724ac-149">Controller</span></span>
* Identity  
* <span data-ttu-id="724ac-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="724ac-150">Razorpage</span></span>
* <span data-ttu-id="724ac-151">檢視</span><span class="sxs-lookup"><span data-stu-id="724ac-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="724ac-152">區域選項</span><span class="sxs-lookup"><span data-stu-id="724ac-152">Area options</span></span>

<span data-ttu-id="724ac-153">此工具旨在用於搭配控制器與檢視使用 ASP.NET Core Web 專案。</span><span class="sxs-lookup"><span data-stu-id="724ac-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="724ac-154">不適用於 Razor 頁面應用程式。</span><span class="sxs-lookup"><span data-stu-id="724ac-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="724ac-155">使用方式：`dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="724ac-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="724ac-156">上述命令會產生下列資料夾：</span><span class="sxs-lookup"><span data-stu-id="724ac-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="724ac-157">*區*</span><span class="sxs-lookup"><span data-stu-id="724ac-157">*Areas*</span></span>
  * <span data-ttu-id="724ac-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="724ac-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="724ac-159">*控制器*</span><span class="sxs-lookup"><span data-stu-id="724ac-159">*Controllers*</span></span>
    * <span data-ttu-id="724ac-160">*Data*</span><span class="sxs-lookup"><span data-stu-id="724ac-160">*Data*</span></span>
    * <span data-ttu-id="724ac-161">*模型*</span><span class="sxs-lookup"><span data-stu-id="724ac-161">*Models*</span></span>
    * <span data-ttu-id="724ac-162">*檢視*</span><span class="sxs-lookup"><span data-stu-id="724ac-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="724ac-163">控制器選項</span><span class="sxs-lookup"><span data-stu-id="724ac-163">Controller options</span></span>

<span data-ttu-id="724ac-164">下表列出和的選項  `aspnet-codegenerator` `controller` `razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="724ac-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="724ac-165">下表列出 `aspnet-codegenerator controller` 的專用選項：</span><span class="sxs-lookup"><span data-stu-id="724ac-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="724ac-166">選項</span><span class="sxs-lookup"><span data-stu-id="724ac-166">Option</span></span>                         | <span data-ttu-id="724ac-167">描述</span><span class="sxs-lookup"><span data-stu-id="724ac-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="724ac-168">--controllerName 或 -name</span><span class="sxs-lookup"><span data-stu-id="724ac-168">--controllerName or -name</span></span>      | <span data-ttu-id="724ac-169">控制器的名稱。</span><span class="sxs-lookup"><span data-stu-id="724ac-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="724ac-170">--useAsyncActions 或 -async</span><span class="sxs-lookup"><span data-stu-id="724ac-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="724ac-171">產生非同步控制器動作。</span><span class="sxs-lookup"><span data-stu-id="724ac-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="724ac-172">--noViews 或 -nv</span><span class="sxs-lookup"><span data-stu-id="724ac-172">--noViews or -nv</span></span>               | <span data-ttu-id="724ac-173">**不** 產生任何檢視。</span><span class="sxs-lookup"><span data-stu-id="724ac-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="724ac-174">--restWithNoViews 或 -api</span><span class="sxs-lookup"><span data-stu-id="724ac-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="724ac-175">使用 REST 樣式 API 產生控制器。</span><span class="sxs-lookup"><span data-stu-id="724ac-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="724ac-176">假設使用 `noViews` 且會忽略所有檢視相關選項。</span><span class="sxs-lookup"><span data-stu-id="724ac-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="724ac-177">--readWriteActions 或 -actions</span><span class="sxs-lookup"><span data-stu-id="724ac-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="724ac-178">在不使用模型的情況下使用讀取/寫入動作產生控制器。</span><span class="sxs-lookup"><span data-stu-id="724ac-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="724ac-179">使用 `-h` 參數取得 `aspnet-codegenerator controller` 命令的說明：</span><span class="sxs-lookup"><span data-stu-id="724ac-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="724ac-180">請參閱[架起電影模型](xref:tutorials/first-mvc-app/adding-model)以取得 `dotnet aspnet-codegenerator controller` 的範例。</span><span class="sxs-lookup"><span data-stu-id="724ac-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="724ac-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="724ac-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="724ac-182">Razor 您可以藉由指定新頁面的名稱和要使用的範本來個別 scaffold 頁面。</span><span class="sxs-lookup"><span data-stu-id="724ac-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="724ac-183">支援的範本為：</span><span class="sxs-lookup"><span data-stu-id="724ac-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="724ac-184">例如，下列命令會使用「編輯」範本來產生 *MyEdit.cshtml* 與 *MyEdit.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="724ac-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="724ac-185">一般而言，不會指定範本與產生的檔案名稱，而且會建立下列範本：</span><span class="sxs-lookup"><span data-stu-id="724ac-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="724ac-186">下表列出和的選項  `aspnet-codegenerator` `razorpage` `controller` ：</span><span class="sxs-lookup"><span data-stu-id="724ac-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="724ac-187">下表列出 `aspnet-codegenerator razorpage` 的專用選項：</span><span class="sxs-lookup"><span data-stu-id="724ac-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="724ac-188">選項</span><span class="sxs-lookup"><span data-stu-id="724ac-188">Option</span></span>                        | <span data-ttu-id="724ac-189">描述</span><span class="sxs-lookup"><span data-stu-id="724ac-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="724ac-190">--namespaceName 或 -namespace</span><span class="sxs-lookup"><span data-stu-id="724ac-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="724ac-191">要用於產生之 PageModel 的命名空間名稱</span><span class="sxs-lookup"><span data-stu-id="724ac-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="724ac-192">--partialView 或 -partial</span><span class="sxs-lookup"><span data-stu-id="724ac-192">--partialView or -partial</span></span>     | <span data-ttu-id="724ac-193">產生部分檢視。</span><span class="sxs-lookup"><span data-stu-id="724ac-193">Generate a partial view.</span></span> <span data-ttu-id="724ac-194">若指定此選項，會忽略版面配置選項 -l 與 -udl。</span><span class="sxs-lookup"><span data-stu-id="724ac-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="724ac-195">--noPageModel 或 -npm</span><span class="sxs-lookup"><span data-stu-id="724ac-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="724ac-196">切換為不產生空白範本的 PageModel 類別</span><span class="sxs-lookup"><span data-stu-id="724ac-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="724ac-197">使用 `-h` 參數取得 `aspnet-codegenerator razorpage` 命令的說明：</span><span class="sxs-lookup"><span data-stu-id="724ac-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="724ac-198">請參閱[架起電影模型](xref:tutorials/razor-pages/model)以取得 `dotnet aspnet-codegenerator razorpage` 的範例。</span><span class="sxs-lookup"><span data-stu-id="724ac-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="724ac-199">請[參閱 Identity Scaffold](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="724ac-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
