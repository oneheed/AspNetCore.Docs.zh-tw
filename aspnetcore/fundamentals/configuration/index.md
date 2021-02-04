---
title: ASP.NET Core 的設定
author: rick-anderson
description: 了解如何使用組態 API 設定 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 62c9d1a58e0f771d91e2bc57f39ec5ebb25baaed
ms.sourcegitcommit: 37186f76e4a50d7fb7389026dd0e5e234b51ebb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2021
ms.locfileid: "99541364"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="03d68-103">ASP.NET Core 的設定</span><span class="sxs-lookup"><span data-stu-id="03d68-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="03d68-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="03d68-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03d68-105">ASP.NET Core 中的設定是使用一或多個設定 [提供者](#cp)來執行。</span><span class="sxs-lookup"><span data-stu-id="03d68-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="03d68-106">設定提供者會使用各種設定來源從機碼值組讀取設定資料：</span><span class="sxs-lookup"><span data-stu-id="03d68-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="03d68-107">設定檔案，例如 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="03d68-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="03d68-108">環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-108">Environment variables</span></span>
* <span data-ttu-id="03d68-109">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="03d68-109">Azure Key Vault</span></span>
* <span data-ttu-id="03d68-110">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="03d68-110">Azure App Configuration</span></span>
* <span data-ttu-id="03d68-111">命令列引數</span><span class="sxs-lookup"><span data-stu-id="03d68-111">Command-line arguments</span></span>
* <span data-ttu-id="03d68-112">自訂提供者、已安裝或已建立</span><span class="sxs-lookup"><span data-stu-id="03d68-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="03d68-113">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-113">Directory files</span></span>
* <span data-ttu-id="03d68-114">記憶體內部 .NET 物件</span><span class="sxs-lookup"><span data-stu-id="03d68-114">In-memory .NET objects</span></span>

<span data-ttu-id="03d68-115">本主題提供 ASP.NET Core 中設定的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="03d68-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="03d68-116">如需在主控台應用程式中使用設定的詳細資訊，請參閱 [.net](/dotnet/core/extensions/configuration)設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="03d68-117">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="03d68-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="03d68-118">預設組態</span><span class="sxs-lookup"><span data-stu-id="03d68-118">Default configuration</span></span>

<span data-ttu-id="03d68-119">ASP.NET Core 使用 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 建立的 web 應用程式會產生下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="03d68-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 會以下列順序提供應用程式的預設組態：</span><span class="sxs-lookup"><span data-stu-id="03d68-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="03d68-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) ：加入現有的 `IConfiguration` 作為來源。</span><span class="sxs-lookup"><span data-stu-id="03d68-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="03d68-122">在預設設定案例中，新增 [主機](#hvac) 設定，並將其設定為 _應用程式_ 設定的第一個來源。</span><span class="sxs-lookup"><span data-stu-id="03d68-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="03d68-123">[appsettings.json](#appsettingsjson) 使用 [JSON 設定提供者](#file-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="03d68-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="03d68-124">*appsettings。* `Environment`使用 [json 設定提供者](#file-configuration-provider)的 *json。*</span><span class="sxs-lookup"><span data-stu-id="03d68-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="03d68-125">例如， *appsettings*。***生產 \* \* _._json* 與 *appsettings*。 \* \* \* 開發** _._json \*。</span><span class="sxs-lookup"><span data-stu-id="03d68-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="03d68-126">應用程式在環境中執行時的[應用程式秘密](xref:security/app-secrets) `Development` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="03d68-127">使用 [環境變數設定提供者](#evcp)的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="03d68-128">使用 [命令列設定提供者](#command-line)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="03d68-129">稍後新增的設定提供者會覆寫先前的金鑰設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="03d68-130">例如，如果 `MyKey` 在和環境中設定 *appsettings.json* ，則會使用環境值。</span><span class="sxs-lookup"><span data-stu-id="03d68-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="03d68-131">使用預設的設定提供者，  [命令列設定提供者](#clcp) 會覆寫所有其他提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="03d68-132">如需的詳細資訊 `CreateDefaultBuilder` ，請參閱 [預設 builder 設定](xref:fundamentals/host/generic-host#default-builder-settings)。</span><span class="sxs-lookup"><span data-stu-id="03d68-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="03d68-133">下列程式碼會依新增的順序顯示已啟用的設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="03d68-134">請考慮下列檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="03d68-135">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-136">預設會 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 以下列順序載入設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="03d68-137">*appsettings。* `Environment`*. json* ：例如 *appsettings*。\*\**生產 \* \* _._json* 和 \*appsettings\*\*\*\* \* \* 開發 _._json \* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="03d68-138">檔案的環境版本是根據 [IHostingEnvironment EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)載入。</span><span class="sxs-lookup"><span data-stu-id="03d68-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="03d68-139">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="03d68-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="03d68-140">*appsettings*... `Environment`*json* 值會覆寫中的索引鍵 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="03d68-141">例如，根據預設：</span><span class="sxs-lookup"><span data-stu-id="03d68-141">For example, by default:</span></span>

* <span data-ttu-id="03d68-142">在開發期間， *appsettings*. \***開發** _._json \* 設定會覆寫在中找到的值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="03d68-143">在生產環境中， *appsettings*. \***生產** _._json \* 設定會覆寫在中找到的值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="03d68-144">例如，將應用程式部署至 Azure 時。</span><span class="sxs-lookup"><span data-stu-id="03d68-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="03d68-145">使用選項模式系結階層式設定資料</span><span class="sxs-lookup"><span data-stu-id="03d68-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="03d68-146">使用 [預設](#default)設定， *appsettings.json* 以及 *appsettings。* `Environment`已啟用 [reloadOnChange： true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)的 *json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="03d68-147">對和 appsettings 進行的變更 *appsettings.json* *。* `Environment`json 設定 [提供者](#jcp)會讀取應用程式啟動 ***後*** 的 *json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="03d68-148">如需新增其他 JSON 設定檔的相關資訊，請參閱本檔中的 [json 設定提供者](#jcp) 。</span><span class="sxs-lookup"><span data-stu-id="03d68-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="03d68-149">合併服務集合</span><span class="sxs-lookup"><span data-stu-id="03d68-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="03d68-150">安全性與使用者秘密</span><span class="sxs-lookup"><span data-stu-id="03d68-150">Security and user secrets</span></span>

<span data-ttu-id="03d68-151">設定資料指導方針：</span><span class="sxs-lookup"><span data-stu-id="03d68-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="03d68-152">永遠不要將密碼或其他敏感性資料儲存在設定提供者程式碼或純文字設定檔中。</span><span class="sxs-lookup"><span data-stu-id="03d68-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="03d68-153">[秘密管理員](xref:security/app-secrets)工具可以用來在開發中儲存秘密。</span><span class="sxs-lookup"><span data-stu-id="03d68-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="03d68-154">不要在開發或測試環境中使用生產環境祕密。</span><span class="sxs-lookup"><span data-stu-id="03d68-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="03d68-155">請在專案外部指定祕密，以防止其意外認可至開放原始碼存放庫。</span><span class="sxs-lookup"><span data-stu-id="03d68-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="03d68-156">根據 [預設](#default)，使用者秘密設定來源會在 JSON 設定來源之後註冊。</span><span class="sxs-lookup"><span data-stu-id="03d68-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="03d68-157">因此，使用者秘密金鑰優先于和 appsettings 中的金鑰 *appsettings.json* *。* `Environment`*. json*。</span><span class="sxs-lookup"><span data-stu-id="03d68-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="03d68-158">如需有關儲存密碼或其他敏感性資料的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="03d68-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="03d68-159"><xref:security/app-secrets>：包含使用環境變數來儲存敏感性資料的建議。</span><span class="sxs-lookup"><span data-stu-id="03d68-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="03d68-160">秘密管理員工具會使用檔案設定 [提供者](#fcp) ，將使用者秘密儲存在本機系統上的 JSON 檔案中。</span><span class="sxs-lookup"><span data-stu-id="03d68-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="03d68-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可安全地儲存 ASP.NET Core 應用程式的應用程式祕密。</span><span class="sxs-lookup"><span data-stu-id="03d68-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="03d68-162">如需詳細資訊，請參閱<xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="03d68-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="03d68-163">環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-163">Environment variables</span></span>

<span data-ttu-id="03d68-164">使用 [預設](#default)設定時，會在 <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 讀取之後從環境變數機碼值組載入設定 *appsettings.json* （ *appsettings）。* `Environment`*. json* 和 [使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03d68-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="03d68-165">因此，從環境讀取的索引鍵值會覆寫 appsettings 中讀取的值 *appsettings.json* *。* `Environment`*. json* 和使用者秘密。</span><span class="sxs-lookup"><span data-stu-id="03d68-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="03d68-166">下列 `set` 命令：</span><span class="sxs-lookup"><span data-stu-id="03d68-166">The following `set` commands:</span></span>

* <span data-ttu-id="03d68-167">在 Windows 上設定 [上述範例](#appsettingsjson) 的環境索引鍵和值。</span><span class="sxs-lookup"><span data-stu-id="03d68-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="03d68-168">使用 [範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)時，測試設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="03d68-169">`dotnet run`命令必須在專案目錄中執行。</span><span class="sxs-lookup"><span data-stu-id="03d68-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="03d68-170">先前的環境設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-170">The preceding environment settings:</span></span>

* <span data-ttu-id="03d68-171">只會在從其設定的命令視窗啟動的進程中設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="03d68-172">使用 Visual Studio 啟動的瀏覽器將無法讀取。</span><span class="sxs-lookup"><span data-stu-id="03d68-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="03d68-173">您可以使用下列的 [setx](/windows-server/administration/windows-commands/setx) 命令，在 Windows 上設定環境機碼和值。</span><span class="sxs-lookup"><span data-stu-id="03d68-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="03d68-174">與不同 `set` 的 `setx` 是，設定會持續保存。</span><span class="sxs-lookup"><span data-stu-id="03d68-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="03d68-175">`/M` 設定系統內容中的變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="03d68-176">如果 `/M` 未使用此參數，則會設定使用者環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="03d68-177">若要測試上述命令是否覆寫 *appsettings.json* 並 *appsettings。* `Environment`*. json*：</span><span class="sxs-lookup"><span data-stu-id="03d68-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="03d68-178">使用 Visual Studio： Exit 並重新啟動 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="03d68-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="03d68-179">使用 CLI：啟動新的命令視窗，然後輸入 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="03d68-180"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>使用字串呼叫以指定環境變數的前置詞：</span><span class="sxs-lookup"><span data-stu-id="03d68-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="03d68-181">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="03d68-181">In the preceding code:</span></span>

* <span data-ttu-id="03d68-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 會在預設設定 [提供者](#default)之後加入。</span><span class="sxs-lookup"><span data-stu-id="03d68-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="03d68-183">如需排序設定提供者的範例，請參閱 [JSON 設定提供者](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="03d68-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="03d68-184">具有前置詞的環境變數會覆 `MyCustomPrefix_` 寫預設的設定 [提供者](#default)。</span><span class="sxs-lookup"><span data-stu-id="03d68-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="03d68-185">這包括沒有前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="03d68-186">讀取設定機碼值組時，會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="03d68-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="03d68-187">下列命令會測試自訂前置詞：</span><span class="sxs-lookup"><span data-stu-id="03d68-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="03d68-188">[預設](#default)設定會載入前面加上和的環境變數和命令列引數 `DOTNET_` `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="03d68-189">和前置詞 `DOTNET_` `ASPNETCORE_` 是由 ASP.NET Core 用於 [主機和應用程式](xref:fundamentals/host/generic-host#host-configuration)設定，但不適用於使用者設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="03d68-190">如需有關主機和應用程式設定的詳細資訊，請參閱 [.Net 泛型主機](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="03d68-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="03d68-191">在 [Azure App Service](https://azure.microsoft.com/services/app-service/)上，選取 [**設定 > 設定**] 頁面上的 [**新增應用程式設定**]。</span><span class="sxs-lookup"><span data-stu-id="03d68-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="03d68-192">Azure App Service 的應用程式設定如下：</span><span class="sxs-lookup"><span data-stu-id="03d68-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="03d68-193">靜態加密，並透過加密通道傳輸。</span><span class="sxs-lookup"><span data-stu-id="03d68-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="03d68-194">公開為環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-194">Exposed as environment variables.</span></span>

<span data-ttu-id="03d68-195">如需詳細資訊，請參閱 [Azure App：使用 Azure 入口網站覆寫應用程式設定](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="03d68-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="03d68-196">如需 Azure 資料庫連接字串的相關資訊，請參閱 [連接字串](#constr) 前置詞。</span><span class="sxs-lookup"><span data-stu-id="03d68-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="03d68-197">環境變數的命名</span><span class="sxs-lookup"><span data-stu-id="03d68-197">Naming of environment variables</span></span>

<span data-ttu-id="03d68-198">環境變數名稱會反映檔案的結構 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="03d68-199">階層中的每個元素會以雙底線分隔 (偏好) 或冒號。</span><span class="sxs-lookup"><span data-stu-id="03d68-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="03d68-200">當元素結構包含陣列時，應該將陣列索引視為這個路徑中的其他元素名稱。</span><span class="sxs-lookup"><span data-stu-id="03d68-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="03d68-201">請考慮下列檔案 *appsettings.json* 及其對等的值（以環境變數表示）。</span><span class="sxs-lookup"><span data-stu-id="03d68-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="03d68-202">**環境變數**</span><span class="sxs-lookup"><span data-stu-id="03d68-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="03d68-203">在 launchSettings.js中設定的環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-203">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="03d68-204">在 *launchSettings.js* 中設定的環境變數會覆寫系統內容中所設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="03d68-205">命令列</span><span class="sxs-lookup"><span data-stu-id="03d68-205">Command-line</span></span>

<span data-ttu-id="03d68-206">使用 [預設](#default) 設定時，會 <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 從命令列引數索引鍵/值組在下列設定來源之後載入設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-206">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="03d68-207">*appsettings.json* 和 *appsettings*。 `Environment`*json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-207">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="03d68-208">開發環境中的[應用程式秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03d68-208">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03d68-209">環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-209">Environment variables.</span></span>

<span data-ttu-id="03d68-210">根據 [預設](#default)，在命令列覆寫設定值設定的設定值會與所有其他設定提供者一起設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-210">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="03d68-211">命令列引數</span><span class="sxs-lookup"><span data-stu-id="03d68-211">Command-line arguments</span></span>

<span data-ttu-id="03d68-212">下列命令會使用下列命令來設定索引鍵和值 `=` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-212">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="03d68-213">下列命令會使用下列命令來設定索引鍵和值 `/` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-213">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="03d68-214">下列命令會使用下列命令來設定索引鍵和值 `--` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-214">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="03d68-215">索引鍵值：</span><span class="sxs-lookup"><span data-stu-id="03d68-215">The key value:</span></span>

* <span data-ttu-id="03d68-216">必須遵循 `=` ，或者 `--` `/` 當值在空格後面時，索引鍵必須有或的前置詞。</span><span class="sxs-lookup"><span data-stu-id="03d68-216">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="03d68-217">如果 `=` 使用，則不需要。</span><span class="sxs-lookup"><span data-stu-id="03d68-217">Isn't required if `=` is used.</span></span> <span data-ttu-id="03d68-218">例如： `MySetting=` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-218">For example, `MySetting=`.</span></span>

<span data-ttu-id="03d68-219">在相同的命令中，不要混用 `=` 與使用空格的索引鍵/值組搭配使用的命令列引數索引鍵/值配對。</span><span class="sxs-lookup"><span data-stu-id="03d68-219">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="03d68-220">切換對應</span><span class="sxs-lookup"><span data-stu-id="03d68-220">Switch mappings</span></span>

<span data-ttu-id="03d68-221">交換器對應允許索引 **鍵** 名稱取代邏輯。</span><span class="sxs-lookup"><span data-stu-id="03d68-221">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="03d68-222">為方法提供切換取代的字典 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 。</span><span class="sxs-lookup"><span data-stu-id="03d68-222">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="03d68-223">使用切換對應字典時，會檢查字典中是否有任何索引鍵符合命令列引數所提供的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="03d68-223">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="03d68-224">如果在字典中找到命令列索引鍵，則會傳回字典值以將索引鍵/值組設定為應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-224">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="03d68-225">所有前面加上單虛線 (`-`) 的命令列索引鍵都需要切換對應。</span><span class="sxs-lookup"><span data-stu-id="03d68-225">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="03d68-226">切換對應字典索引鍵規則：</span><span class="sxs-lookup"><span data-stu-id="03d68-226">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="03d68-227">參數的開頭必須是 `-` 或 `--` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-227">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="03d68-228">切換對應字典不能包含重複索引鍵。</span><span class="sxs-lookup"><span data-stu-id="03d68-228">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="03d68-229">若要使用切換對應字典，請將它傳遞到的呼叫中 `AddCommandLine` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-229">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="03d68-230">下列程式碼顯示已取代金鑰的索引鍵值：</span><span class="sxs-lookup"><span data-stu-id="03d68-230">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-231">下列命令適用于測試金鑰取代：</span><span class="sxs-lookup"><span data-stu-id="03d68-231">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="03d68-232">針對使用切換對應的應用程式，呼叫 `CreateDefaultBuilder` 不應傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-232">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="03d68-233">`CreateDefaultBuilder`方法的 `AddCommandLine` 呼叫不包含對應的參數，而且沒有任何方法可將切換對應字典傳遞給 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-233">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03d68-234">解決方案不會將引數傳遞至， `CreateDefaultBuilder` 而是會允許 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法處理引數和切換對應字典。</span><span class="sxs-lookup"><span data-stu-id="03d68-234">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="03d68-235">階層式設定資料</span><span class="sxs-lookup"><span data-stu-id="03d68-235">Hierarchical configuration data</span></span>

<span data-ttu-id="03d68-236">設定 API 會透過使用設定金鑰中的分隔符號來簡維階層式資料，以讀取階層式設定資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-236">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="03d68-237">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-237">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="03d68-238">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示數個設定設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-238">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-239">讀取階層式設定資料的慣用方式是使用選項模式。</span><span class="sxs-lookup"><span data-stu-id="03d68-239">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="03d68-240">如需詳細資訊，請參閱本檔中的系結階層式設定 [資料](#optpat) 。</span><span class="sxs-lookup"><span data-stu-id="03d68-240">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="03d68-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 與 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用來在設定資料中隔離區段與區段的子系。</span><span class="sxs-lookup"><span data-stu-id="03d68-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="03d68-242">[GetSection,、 GetChildren 與 Exists](#getsection) 中說明這些方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-242">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="03d68-243">設定機碼和值</span><span class="sxs-lookup"><span data-stu-id="03d68-243">Configuration keys and values</span></span>

<span data-ttu-id="03d68-244">設定金鑰：</span><span class="sxs-lookup"><span data-stu-id="03d68-244">Configuration keys:</span></span>

* <span data-ttu-id="03d68-245">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="03d68-245">Are case-insensitive.</span></span> <span data-ttu-id="03d68-246">例如，`ConnectionString` 與 `connectionstring` 會被視為相等的機碼。</span><span class="sxs-lookup"><span data-stu-id="03d68-246">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="03d68-247">如果在多個設定提供者中設定索引鍵和值，則會使用最後新增的提供者所提供的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-247">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="03d68-248">如需詳細資訊，請參閱 [預設](#default)設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-248">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="03d68-249">階層式機碼</span><span class="sxs-lookup"><span data-stu-id="03d68-249">Hierarchical keys</span></span>
  * <span data-ttu-id="03d68-250">在設定 API 內，冒號分隔字元 (`:`) 可在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="03d68-250">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="03d68-251">在環境變數中，冒號分隔字元可能無法在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="03d68-251">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="03d68-252">所有平臺都支援雙底線、 `__` ，而且會自動轉換為冒號 `:` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-252">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="03d68-253">在 Azure Key Vault 中，階層式索引鍵會使用 `--` 做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="03d68-253">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="03d68-254">[](xref:security/key-vault-configuration) `--` `:` 當秘密載入至應用程式的設定時，Azure Key Vault 設定提供者會自動取代為。</span><span class="sxs-lookup"><span data-stu-id="03d68-254">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="03d68-255"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支援在設定機碼中使用陣列索引將陣列繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="03d68-255">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03d68-256">[將陣列繫結到類別](#boa)一節說明陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="03d68-256">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="03d68-257">設定值：</span><span class="sxs-lookup"><span data-stu-id="03d68-257">Configuration values:</span></span>

* <span data-ttu-id="03d68-258">為字串。</span><span class="sxs-lookup"><span data-stu-id="03d68-258">Are strings.</span></span>
* <span data-ttu-id="03d68-259">Null 值無法存放在設定中或繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="03d68-259">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="03d68-260">設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-260">Configuration providers</span></span>

<span data-ttu-id="03d68-261">下表顯示可供 ASP.NET Core 應用程式使用的設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-261">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="03d68-262">提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-262">Provider</span></span> | <span data-ttu-id="03d68-263">從提供設定</span><span class="sxs-lookup"><span data-stu-id="03d68-263">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="03d68-264">Azure Key Vault 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-264">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="03d68-265">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="03d68-265">Azure Key Vault</span></span> |
| [<span data-ttu-id="03d68-266">Azure App 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-266">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="03d68-267">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="03d68-267">Azure App Configuration</span></span> |
| [<span data-ttu-id="03d68-268">命令列設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-268">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="03d68-269">命令列參數</span><span class="sxs-lookup"><span data-stu-id="03d68-269">Command-line parameters</span></span> |
| [<span data-ttu-id="03d68-270">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-270">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="03d68-271">自訂來源</span><span class="sxs-lookup"><span data-stu-id="03d68-271">Custom source</span></span> |
| [<span data-ttu-id="03d68-272">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-272">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="03d68-273">環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-273">Environment variables</span></span> |
| [<span data-ttu-id="03d68-274">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-274">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="03d68-275">INI、JSON 和 XML 檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-275">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="03d68-276">每個檔案的金鑰配置提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-276">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="03d68-277">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-277">Directory files</span></span> |
| [<span data-ttu-id="03d68-278">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-278">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="03d68-279">記憶體內集合</span><span class="sxs-lookup"><span data-stu-id="03d68-279">In-memory collections</span></span> |
| [<span data-ttu-id="03d68-280">使用者祕密</span><span class="sxs-lookup"><span data-stu-id="03d68-280">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="03d68-281">使用者設定檔目錄中的檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-281">File in the user profile directory</span></span> |

<span data-ttu-id="03d68-282">設定來源會依照其設定提供者的指定順序讀取。</span><span class="sxs-lookup"><span data-stu-id="03d68-282">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="03d68-283">在程式碼中訂購設定提供者，以符合應用程式所需之基礎設定來源的優先順序。</span><span class="sxs-lookup"><span data-stu-id="03d68-283">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="03d68-284">典型的設定提供者順序是：</span><span class="sxs-lookup"><span data-stu-id="03d68-284">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="03d68-285">*appsettings*... `Environment`*json*</span><span class="sxs-lookup"><span data-stu-id="03d68-285">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="03d68-286">使用者祕密</span><span class="sxs-lookup"><span data-stu-id="03d68-286">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="03d68-287">使用 [環境變數設定提供者](#evcp)的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-287">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="03d68-288">使用 [命令列設定提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-288">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="03d68-289">常見的作法是將命令列設定提供者新增至一系列的提供者，以允許命令列引數覆寫其他提供者所設定的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-289">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="03d68-290">先前的提供者序列會用於 [預設](#default)設定中。</span><span class="sxs-lookup"><span data-stu-id="03d68-290">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="03d68-291">連接字串前置詞</span><span class="sxs-lookup"><span data-stu-id="03d68-291">Connection string prefixes</span></span>

<span data-ttu-id="03d68-292">設定 API 有四個連接字串環境變數的特殊處理規則。</span><span class="sxs-lookup"><span data-stu-id="03d68-292">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="03d68-293">這些連接字串涉及設定應用程式環境的 Azure 連接字串。</span><span class="sxs-lookup"><span data-stu-id="03d68-293">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="03d68-294">具有資料表中所顯示前置詞的環境變數會以 [預設](#default) 設定載入至應用程式，或不提供任何前置詞 `AddEnvironmentVariables` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-294">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="03d68-295">連接字串前置詞</span><span class="sxs-lookup"><span data-stu-id="03d68-295">Connection string prefix</span></span> | <span data-ttu-id="03d68-296">提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-296">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="03d68-297">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-297">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="03d68-298">MySQL</span><span class="sxs-lookup"><span data-stu-id="03d68-298">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="03d68-299">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="03d68-299">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="03d68-300">SQL Server</span><span class="sxs-lookup"><span data-stu-id="03d68-300">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="03d68-301">當探索到具有下表所顯示之任何四個前置詞的環境變數並將其載入到設定中時：</span><span class="sxs-lookup"><span data-stu-id="03d68-301">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="03d68-302">會透過移除環境變數前置詞並新增設定機碼區段 (`ConnectionStrings`) 來移除具有下表顯示之前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-302">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="03d68-303">會建立新的設定機碼值組以代表資料庫連線提供者 (`CUSTOMCONNSTR_` 除外，這沒有所述提供者)。</span><span class="sxs-lookup"><span data-stu-id="03d68-303">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="03d68-304">環境變數機碼</span><span class="sxs-lookup"><span data-stu-id="03d68-304">Environment variable key</span></span> | <span data-ttu-id="03d68-305">已轉換的設定機碼</span><span class="sxs-lookup"><span data-stu-id="03d68-305">Converted configuration key</span></span> | <span data-ttu-id="03d68-306">提供者設定項目</span><span class="sxs-lookup"><span data-stu-id="03d68-306">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-307">設定項目未建立。</span><span class="sxs-lookup"><span data-stu-id="03d68-307">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-308">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="03d68-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03d68-309">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="03d68-309">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-310">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="03d68-310">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03d68-311">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="03d68-311">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-312">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="03d68-312">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03d68-313">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="03d68-313">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="03d68-314">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-314">File configuration provider</span></span>

<span data-ttu-id="03d68-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是用於從檔案系統載入設定的基底類別。</span><span class="sxs-lookup"><span data-stu-id="03d68-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="03d68-316">以下是衍生自的設定提供者 `FileConfigurationProvider` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-316">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="03d68-317">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-317">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="03d68-318">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-318">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="03d68-319">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-319">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="03d68-320">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-320">INI configuration provider</span></span>

<span data-ttu-id="03d68-321"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 會在執行階段從 INI 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-321">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="03d68-322">下列程式碼會清除所有設定提供者，並新增數個設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="03d68-323">在上述程式碼中， *MyIniConfig.ini* 和  *MyIniConfig* 中的設定 `Environment` 。中的設定會覆寫 *ini* 檔案：</span><span class="sxs-lookup"><span data-stu-id="03d68-323">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="03d68-324">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-324">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="03d68-325">[命令列設定提供者](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="03d68-325">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03d68-326">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列 *MyIniConfig.ini* 檔案：</span><span class="sxs-lookup"><span data-stu-id="03d68-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="03d68-327">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="03d68-328">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-328">JSON configuration provider</span></span>

<span data-ttu-id="03d68-329">會 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 從 JSON 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-329">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="03d68-330">多載可以指定：</span><span class="sxs-lookup"><span data-stu-id="03d68-330">Overloads can specify:</span></span>

* <span data-ttu-id="03d68-331">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="03d68-331">Whether the file is optional.</span></span>
* <span data-ttu-id="03d68-332">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-332">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="03d68-333">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-333">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="03d68-334">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-334">The preceding code:</span></span>

* <span data-ttu-id="03d68-335">設定 JSON 設定提供者，以使用下列選項來載入檔案 *上的MyConfig.js* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-335">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="03d68-336">`optional: true`：檔案是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="03d68-336">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="03d68-337">`reloadOnChange: true` ：儲存變更時，會重載該檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-337">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="03d68-338">在 *MyConfig.json* 檔案之前讀取 [預設設定提供者](#default)。</span><span class="sxs-lookup"><span data-stu-id="03d68-338">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="03d68-339">預設設定提供者中的 [檔案覆寫] *MyConfig.js* 設定，包括 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="03d68-339">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03d68-340">您通常 ***不*** 希望自訂 JSON 檔案覆寫 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)中所設定的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-340">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03d68-341">下列程式碼會清除所有設定提供者，並新增數個設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-341">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="03d68-342">在上述程式碼中， *MyConfig.js* 中的設定和 *myconfig.xml*。 `Environment`*json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="03d68-342">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="03d68-343">覆寫 *appsettings.json* 和 *appsettings* `Environment` 中的設定。*json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-343">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="03d68-344">由 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)中的設定覆寫。</span><span class="sxs-lookup"><span data-stu-id="03d68-344">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03d68-345">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含檔案上的下列 *MyConfig.js* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-345">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="03d68-346">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-346">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="03d68-347">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-347">XML configuration provider</span></span>

<span data-ttu-id="03d68-348"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 會在執行階段從 XML 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-348">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="03d68-349">下列程式碼會清除所有設定提供者，並新增數個設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="03d68-350">在上述程式碼中， *MyXMLFile.xml* 和  *MyXMLFile* 中的設定 `Environment` 。中的設定會覆寫 *xml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="03d68-350">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="03d68-351">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="03d68-352">[命令列設定提供者](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="03d68-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03d68-353">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列 *MyXMLFile.xml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="03d68-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="03d68-354">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-355">若 `name` 屬性是用來區別元素，則可以重複那些使用相同元素名稱的元素：</span><span class="sxs-lookup"><span data-stu-id="03d68-355">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="03d68-356">下列程式碼會讀取先前的設定檔，並顯示金鑰和值：</span><span class="sxs-lookup"><span data-stu-id="03d68-356">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-357">屬性可用來提供值：</span><span class="sxs-lookup"><span data-stu-id="03d68-357">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="03d68-358">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-358">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03d68-359">key:attribute</span><span class="sxs-lookup"><span data-stu-id="03d68-359">key:attribute</span></span>
* <span data-ttu-id="03d68-360">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="03d68-360">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="03d68-361">每個檔案的金鑰配置提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-361">Key-per-file configuration provider</span></span>

<span data-ttu-id="03d68-362"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目錄的檔案做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-362">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="03d68-363">機碼是檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="03d68-363">The key is the file name.</span></span> <span data-ttu-id="03d68-364">值包含檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="03d68-364">The value contains the file's contents.</span></span> <span data-ttu-id="03d68-365">每個檔案的每個檔案設定提供者都是在 Docker 裝載案例中使用。</span><span class="sxs-lookup"><span data-stu-id="03d68-365">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="03d68-366">若要啟用每個檔案機碼設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-366">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="03d68-367">檔案的 `directoryPath` 必須是絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="03d68-367">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="03d68-368">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="03d68-368">Overloads permit specifying:</span></span>

* <span data-ttu-id="03d68-369">設定來源的 `Action<KeyPerFileConfigurationSource>` 委派。</span><span class="sxs-lookup"><span data-stu-id="03d68-369">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="03d68-370">目錄是否為選擇性與目錄的路徑。</span><span class="sxs-lookup"><span data-stu-id="03d68-370">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="03d68-371">雙底線 (`__`) 是做為檔案名稱中的設定金鑰分隔符號使用。</span><span class="sxs-lookup"><span data-stu-id="03d68-371">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="03d68-372">例如，檔案名稱 `Logging__LogLevel__System` 會產生設定金鑰 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="03d68-372">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="03d68-373">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-373">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="03d68-374">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-374">Memory configuration provider</span></span>

<span data-ttu-id="03d68-375"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用記憶體內集合做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-375">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="03d68-376">下列程式碼會將記憶體集合新增至設定系統：</span><span class="sxs-lookup"><span data-stu-id="03d68-376">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="03d68-377">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述設定設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-377">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-378">在上述程式碼中， `config.AddInMemoryCollection(Dict)` 是在 [預設設定提供者](#default)之後加入。</span><span class="sxs-lookup"><span data-stu-id="03d68-378">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="03d68-379">如需排序設定提供者的範例，請參閱 [JSON 設定提供者](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="03d68-379">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="03d68-380">請參閱使用 [將陣列](#boa) 系結到另一個範例 `MemoryConfigurationProvider` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-380">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="03d68-381">GetValue</span><span class="sxs-lookup"><span data-stu-id="03d68-381">GetValue</span></span>

<span data-ttu-id="03d68-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 從具有指定索引鍵的設定中解壓縮單一值，並將其轉換為指定的類型：</span><span class="sxs-lookup"><span data-stu-id="03d68-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-383">在上述程式碼中，如果在設定 `NumberKey` 中找不到， `99` 就會使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="03d68-383">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="03d68-384">GetSection、GetChildren 與 Exists</span><span class="sxs-lookup"><span data-stu-id="03d68-384">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="03d68-385">針對接下來的範例，請考慮下列 *MySubsection.json* file：</span><span class="sxs-lookup"><span data-stu-id="03d68-385">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="03d68-386">下列程式碼會將 *MySubsection.js* 新增至設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-386">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="03d68-387">GetSection</span><span class="sxs-lookup"><span data-stu-id="03d68-387">GetSection</span></span>

<span data-ttu-id="03d68-388">[IConfiguration](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 會傳回具有指定之子區段索引鍵的設定子區段。</span><span class="sxs-lookup"><span data-stu-id="03d68-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="03d68-389">下列程式碼會傳回的值 `section1` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-389">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-390">下列程式碼會傳回的值 `section2:subsection0` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-390">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-391">`GetSection` 絕不會傳回 `null`。</span><span class="sxs-lookup"><span data-stu-id="03d68-391">`GetSection` never returns `null`.</span></span> <span data-ttu-id="03d68-392">若找不到相符的區段，會傳回空的 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="03d68-392">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="03d68-393">當 `GetSection` 傳回相符區段時，未填入 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>。</span><span class="sxs-lookup"><span data-stu-id="03d68-393">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="03d68-394">當區段存在時，會傳回 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 與 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>。</span><span class="sxs-lookup"><span data-stu-id="03d68-394">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="03d68-395">GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="03d68-395">GetChildren and Exists</span></span>

<span data-ttu-id="03d68-396">下列程式碼會呼叫 [IConfiguration GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) ，並傳回 `section2:subsection0` 下列值：</span><span class="sxs-lookup"><span data-stu-id="03d68-396">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-397">上述程式碼會呼叫 [>microsoft.extensions.options.configurationextensions](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) ，以確認區段存在：</span><span class="sxs-lookup"><span data-stu-id="03d68-397">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="03d68-398">系結陣列</span><span class="sxs-lookup"><span data-stu-id="03d68-398">Bind an array</span></span>

<span data-ttu-id="03d68-399">[ConfigurationBinder](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)支援使用設定索引鍵中的陣列索引，將陣列系結至物件。</span><span class="sxs-lookup"><span data-stu-id="03d68-399">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03d68-400">任何公開數位索引鍵區段的陣列格式都能夠將陣列系結至 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 類別陣列。</span><span class="sxs-lookup"><span data-stu-id="03d68-400">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="03d68-401">請考慮從 [範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中 *MyArray.js* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-401">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="03d68-402">下列程式碼會將 *MyArray.js* 新增至設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-402">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="03d68-403">下列程式碼會讀取設定，並顯示這些值：</span><span class="sxs-lookup"><span data-stu-id="03d68-403">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-404">上述程式碼會傳回下列輸出：</span><span class="sxs-lookup"><span data-stu-id="03d68-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="03d68-405">在上述輸出中，索引3具有值 `value40` ，對應于 `"4": "value40",` *MyArray.js* 中的。</span><span class="sxs-lookup"><span data-stu-id="03d68-405">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="03d68-406">系結陣列索引是連續的，不會系結至設定索引鍵索引。</span><span class="sxs-lookup"><span data-stu-id="03d68-406">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="03d68-407">設定系結器無法系結 null 值，或在系結物件中建立 null 專案</span><span class="sxs-lookup"><span data-stu-id="03d68-407">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="03d68-408">下列程式碼會 `array:entries` 使用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 擴充方法載入設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-408">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="03d68-409">下列程式碼會讀取中的設定 `arrayDict` `Dictionary` ，並顯示這些值：</span><span class="sxs-lookup"><span data-stu-id="03d68-409">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-410">上述程式碼會傳回下列輸出：</span><span class="sxs-lookup"><span data-stu-id="03d68-410">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="03d68-411">繫結物件中的索引 &num;3 存放 `array:4` 設定機碼與其 `value4` 的設定資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-411">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="03d68-412">系結包含陣列的設定資料時，在建立物件時，會使用設定機碼中的陣列索引來反覆運算設定資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-412">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="03d68-413">設定資料中不能保留 Null 值，當設定機碼中的陣列略過一或多個索引時，不會在繫結物件中建立 Null 值項目。</span><span class="sxs-lookup"><span data-stu-id="03d68-413">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="03d68-414">在系 &num; 結至實例之前，您可以藉 `ArrayExample` 由讀取索引 &num; 3 索引鍵/值組的任何設定提供者，在系結至實例之前，提供索引3的遺漏設定專案。</span><span class="sxs-lookup"><span data-stu-id="03d68-414">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="03d68-415">請考慮下列來自範例下載的檔案 *Value3.js* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-415">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="03d68-416">下列程式碼包含與的 *Value3.js* 設定 `arrayDict` `Dictionary` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-416">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="03d68-417">下列程式碼會讀取先前的設定，並顯示這些值：</span><span class="sxs-lookup"><span data-stu-id="03d68-417">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-418">上述程式碼會傳回下列輸出：</span><span class="sxs-lookup"><span data-stu-id="03d68-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="03d68-419">自訂設定提供者不需要實作陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="03d68-419">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="03d68-420">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-420">Custom configuration provider</span></span>

<span data-ttu-id="03d68-421">範例應用程式示範如何建立會使用 [Entity Framework (EF)](/ef/core/) 從資料庫讀取設定機碼值組的基本設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-421">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="03d68-422">提供者具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="03d68-422">The provider has the following characteristics:</span></span>

* <span data-ttu-id="03d68-423">EF 記憶體內資料庫會用於示範用途。</span><span class="sxs-lookup"><span data-stu-id="03d68-423">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="03d68-424">若要使用需要連接字串的資料庫，請實作第二個 `ConfigurationBuilder` 以從另一個設定提供者提供連接字串。</span><span class="sxs-lookup"><span data-stu-id="03d68-424">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="03d68-425">啟動時，該提供者會將資料庫資料表讀入到設定中。</span><span class="sxs-lookup"><span data-stu-id="03d68-425">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="03d68-426">該提供者不會以個別機碼為基礎查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="03d68-426">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="03d68-427">未實作變更時重新載入，因此在應用程式啟動後更新資料庫對應用程式設定沒有影響。</span><span class="sxs-lookup"><span data-stu-id="03d68-427">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="03d68-428">定義 `EFConfigurationValue` 實體來在資料庫中存放設定值。</span><span class="sxs-lookup"><span data-stu-id="03d68-428">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="03d68-429">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-429">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="03d68-430">新增 `EFConfigurationContext` 以存放及存取已設定的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-430">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="03d68-431">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="03d68-432">建立會實作 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的類別。</span><span class="sxs-lookup"><span data-stu-id="03d68-432">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="03d68-433">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="03d68-434">透過繼承自 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 來建立自訂設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-434">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="03d68-435">若資料庫是空的，設定提供者會初始化資料庫。</span><span class="sxs-lookup"><span data-stu-id="03d68-435">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="03d68-436">由於設定索引 [鍵不區分大小寫](#keys)，因此用來初始化資料庫的字典會以不區分大小寫的比較子來建立 ([stringcomparison. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 。</span><span class="sxs-lookup"><span data-stu-id="03d68-436">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="03d68-437">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="03d68-438">`AddEFConfiguration` 擴充方法允許新增設定來源到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="03d68-438">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="03d68-439">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="03d68-439">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="03d68-440">下列程式碼顯示如何在 *Program.cs* 中使用自訂 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="03d68-440">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="03d68-441">在啟動時存取設定</span><span class="sxs-lookup"><span data-stu-id="03d68-441">Access configuration in Startup</span></span>

<span data-ttu-id="03d68-442">下列程式碼會在方法中顯示設定資料 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-442">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="03d68-443">如需使用啟動方便方法來存取設定的範例，請參閱[應用程式啟動：方便方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="03d68-443">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="03d68-444">存取頁面中的設定 Razor</span><span class="sxs-lookup"><span data-stu-id="03d68-444">Access configuration in Razor Pages</span></span>

<span data-ttu-id="03d68-445">下列程式碼會在頁面中顯示設定資料 Razor ：</span><span class="sxs-lookup"><span data-stu-id="03d68-445">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="03d68-446">在下列程式碼中， `MyOptions` 會新增至服務容器， <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 並系結至設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-446">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="03d68-447">下列標記會使用指示詞 [`@inject`](xref:mvc/views/razor#inject) Razor 來解析和顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="03d68-447">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="03d68-448">MVC view 檔案中的存取設定</span><span class="sxs-lookup"><span data-stu-id="03d68-448">Access configuration in a MVC view file</span></span>

<span data-ttu-id="03d68-449">下列程式碼會顯示 MVC 視圖中的設定資料：</span><span class="sxs-lookup"><span data-stu-id="03d68-449">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="03d68-450">使用委派設定選項</span><span class="sxs-lookup"><span data-stu-id="03d68-450">Configure options with a delegate</span></span>

<span data-ttu-id="03d68-451">在委派中設定的選項會覆寫設定提供者中所設定的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-451">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="03d68-452">使用委派來設定選項，會示範為範例應用程式中的範例2。</span><span class="sxs-lookup"><span data-stu-id="03d68-452">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="03d68-453">在下列程式碼中， <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務會新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="03d68-453">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="03d68-454">它會使用委派來設定下列值 `MyOptions` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-454">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="03d68-455">下列程式碼會顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="03d68-455">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="03d68-456">在上述範例中，和的值 `Option1` `Option2` 是在中指定， *appsettings.json* 然後由設定的委派覆寫。</span><span class="sxs-lookup"><span data-stu-id="03d68-456">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="03d68-457">主機與應用程式組態的比較</span><span class="sxs-lookup"><span data-stu-id="03d68-457">Host versus app configuration</span></span>

<span data-ttu-id="03d68-458">設定及啟動應用程式之前，會先設定及啟動「主機」。</span><span class="sxs-lookup"><span data-stu-id="03d68-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="03d68-459">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="03d68-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="03d68-460">應用程式與主機都是使用此主題中所述的設定提供者來設定的。</span><span class="sxs-lookup"><span data-stu-id="03d68-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="03d68-461">主機組態機碼/值組也會包含在應用程式的組態中。</span><span class="sxs-lookup"><span data-stu-id="03d68-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="03d68-462">如需有關當建置主機時如何使用設定提供者的詳細資訊，以及設定來源如何影響主機設定的詳細資訊，請參閱 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="03d68-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="03d68-463">預設主機設定</span><span class="sxs-lookup"><span data-stu-id="03d68-463">Default host configuration</span></span>

<span data-ttu-id="03d68-464">如需使用 [Web 主機](xref:fundamentals/host/web-host)時預設組態的詳細資料，請參閱[本主題的 ASP.NET Core 2.2 版本](?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="03d68-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="03d68-465">主機組態的提供來源：</span><span class="sxs-lookup"><span data-stu-id="03d68-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="03d68-466">前面加 `DOTNET_` 上 (的環境變數，例如， `DOTNET_ENVIRONMENT` 使用 [環境變數設定提供者](#environment-variables)) 。</span><span class="sxs-lookup"><span data-stu-id="03d68-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="03d68-467">載入設定機碼值組時，會移除前置詞 (`DOTNET_`)。</span><span class="sxs-lookup"><span data-stu-id="03d68-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="03d68-468">使用 [命令列設定提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="03d68-469">已建立 Web 主機預設組態 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="03d68-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="03d68-470">Kestrel 會用作為網頁伺服器，並使用應用程式的組態提供者來設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="03d68-471">新增主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="03d68-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="03d68-472">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境變數設定為 `true`，則會新增轉接的標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="03d68-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="03d68-473">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="03d68-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="03d68-474">其他設定</span><span class="sxs-lookup"><span data-stu-id="03d68-474">Other configuration</span></span>

<span data-ttu-id="03d68-475">本主題僅適用于 *應用程式* 設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="03d68-476">執行和主控 ASP.NET Core 應用程式的其他層面，是使用本主題未涵蓋的設定檔來設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="03d68-477">*launch.js開啟* /*launchSettings.js* 為開發環境的工具設定檔，如下所述：</span><span class="sxs-lookup"><span data-stu-id="03d68-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="03d68-478">在中 <xref:fundamentals/environments#development> 。</span><span class="sxs-lookup"><span data-stu-id="03d68-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="03d68-479">在檔集中，用來設定開發案例 ASP.NET Core 應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="03d68-480">*web.config* 是伺服器設定檔，如下列主題所述：</span><span class="sxs-lookup"><span data-stu-id="03d68-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="03d68-481">在 *launchSettings.js* 中設定的環境變數會覆寫系統內容中所設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-481">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="03d68-482">如需從舊版 ASP.NET 遷移應用程式設定的詳細資訊，請參閱 <xref:migration/proper-to-2x/index#store-configurations> 。</span><span class="sxs-lookup"><span data-stu-id="03d68-482">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="03d68-483">從外部組件新增設定</span><span class="sxs-lookup"><span data-stu-id="03d68-483">Add configuration from an external assembly</span></span>

<span data-ttu-id="03d68-484"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 實作允許在啟動時從應用程式 `Startup` 類別外部的外部組件，針對應用程式新增增強功能。</span><span class="sxs-lookup"><span data-stu-id="03d68-484">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="03d68-485">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="03d68-485">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03d68-486">其他資源</span><span class="sxs-lookup"><span data-stu-id="03d68-486">Additional resources</span></span>

* [<span data-ttu-id="03d68-487">設定來源程式碼</span><span class="sxs-lookup"><span data-stu-id="03d68-487">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03d68-488">ASP.NET Core 中的應用程式設定是以由 *設定提供者* 所建立的機碼值組為基礎。</span><span class="sxs-lookup"><span data-stu-id="03d68-488">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="03d68-489">設定提供者會從各種設定來源將設定資料讀取到機碼值組中：</span><span class="sxs-lookup"><span data-stu-id="03d68-489">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="03d68-490">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="03d68-490">Azure Key Vault</span></span>
* <span data-ttu-id="03d68-491">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="03d68-491">Azure App Configuration</span></span>
* <span data-ttu-id="03d68-492">命令列引數</span><span class="sxs-lookup"><span data-stu-id="03d68-492">Command-line arguments</span></span>
* <span data-ttu-id="03d68-493">自訂提供者 (已安裝或已建立)</span><span class="sxs-lookup"><span data-stu-id="03d68-493">Custom providers (installed or created)</span></span>
* <span data-ttu-id="03d68-494">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-494">Directory files</span></span>
* <span data-ttu-id="03d68-495">環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-495">Environment variables</span></span>
* <span data-ttu-id="03d68-496">記憶體內部 .NET 物件</span><span class="sxs-lookup"><span data-stu-id="03d68-496">In-memory .NET objects</span></span>
* <span data-ttu-id="03d68-497">設定檔</span><span class="sxs-lookup"><span data-stu-id="03d68-497">Settings files</span></span>

<span data-ttu-id="03d68-498">一般組態提供者案例 ([microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) 的組態套件包含在 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="03d68-498">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="03d68-499">下列程式碼範例與範例應用程式中的程式碼範例使用 <xref:Microsoft.Extensions.Configuration> 命名空間：</span><span class="sxs-lookup"><span data-stu-id="03d68-499">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="03d68-500">*選項模式* 是此主題中所述之設定概念的延伸。</span><span class="sxs-lookup"><span data-stu-id="03d68-500">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="03d68-501">選項使用類別來代表一組相關的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-501">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="03d68-502">如需詳細資訊，請參閱<xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="03d68-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="03d68-503">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="03d68-503">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="03d68-504">主機與應用程式組態的比較</span><span class="sxs-lookup"><span data-stu-id="03d68-504">Host versus app configuration</span></span>

<span data-ttu-id="03d68-505">設定及啟動應用程式之前，會先設定及啟動「主機」。</span><span class="sxs-lookup"><span data-stu-id="03d68-505">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="03d68-506">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="03d68-506">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="03d68-507">應用程式與主機都是使用此主題中所述的設定提供者來設定的。</span><span class="sxs-lookup"><span data-stu-id="03d68-507">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="03d68-508">主機組態機碼/值組也會包含在應用程式的組態中。</span><span class="sxs-lookup"><span data-stu-id="03d68-508">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="03d68-509">如需有關當建置主機時如何使用設定提供者的詳細資訊，以及設定來源如何影響主機設定的詳細資訊，請參閱 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="03d68-509">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="03d68-510">其他設定</span><span class="sxs-lookup"><span data-stu-id="03d68-510">Other configuration</span></span>

<span data-ttu-id="03d68-511">本主題僅適用于 *應用程式* 設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-511">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="03d68-512">執行和主控 ASP.NET Core 應用程式的其他層面，是使用本主題未涵蓋的設定檔來設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-512">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="03d68-513">*launch.js開啟* /*launchSettings.js* 為開發環境的工具設定檔，如下所述：</span><span class="sxs-lookup"><span data-stu-id="03d68-513">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="03d68-514">在中 <xref:fundamentals/environments#development> 。</span><span class="sxs-lookup"><span data-stu-id="03d68-514">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="03d68-515">在檔集中，用來設定開發案例 ASP.NET Core 應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-515">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="03d68-516">*web.config* 是伺服器設定檔，如下列主題所述：</span><span class="sxs-lookup"><span data-stu-id="03d68-516">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="03d68-517">如需從舊版 ASP.NET 遷移應用程式設定的詳細資訊，請參閱 <xref:migration/proper-to-2x/index#store-configurations> 。</span><span class="sxs-lookup"><span data-stu-id="03d68-517">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="03d68-518">預設組態</span><span class="sxs-lookup"><span data-stu-id="03d68-518">Default configuration</span></span>

<span data-ttu-id="03d68-519">以 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 範本為基礎的 Web 應用程式，會在建置主機時呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="03d68-519">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="03d68-520">`CreateDefaultBuilder` 會以下列順序提供應用程式的預設組態：</span><span class="sxs-lookup"><span data-stu-id="03d68-520">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="03d68-521">下列項目適用於使用 [Web 主機](xref:fundamentals/host/web-host)的應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-521">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="03d68-522">如需使用[一般主機](xref:fundamentals/host/generic-host)時預設組態的詳細資料，請參閱[本主題的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="03d68-522">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="03d68-523">主機組態的提供來源：</span><span class="sxs-lookup"><span data-stu-id="03d68-523">Host configuration is provided from:</span></span>
  * <span data-ttu-id="03d68-524">使用[環境變數組態提供者](#environment-variables-configuration-provider)且以 `ASPNETCORE_` 為前置詞 (例如 `ASPNETCORE_ENVIRONMENT`) 的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-524">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="03d68-525">載入設定機碼值組時，會移除前置詞 (`ASPNETCORE_`)。</span><span class="sxs-lookup"><span data-stu-id="03d68-525">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="03d68-526">使用[命令列組態提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="03d68-527">應用程式設定的提供來源：</span><span class="sxs-lookup"><span data-stu-id="03d68-527">App configuration is provided from:</span></span>
  * <span data-ttu-id="03d68-528">*appsettings.json* 使用檔案設定 [提供者](#file-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="03d68-528">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="03d68-529">使用 [檔案組態提供者](#file-configuration-provider)的 *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="03d68-529">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="03d68-530">應用程式在使用輸入組件的 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03d68-530">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="03d68-531">使用[環境變數組態提供者](#environment-variables-configuration-provider)的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-531">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="03d68-532">使用[命令列組態提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-532">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="03d68-533">安全性</span><span class="sxs-lookup"><span data-stu-id="03d68-533">Security</span></span>

<span data-ttu-id="03d68-534">採用下列做法來保護敏感性組態資料：</span><span class="sxs-lookup"><span data-stu-id="03d68-534">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="03d68-535">永遠不要將密碼或其他敏感性資料儲存在設定提供者程式碼或純文字設定檔中。</span><span class="sxs-lookup"><span data-stu-id="03d68-535">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="03d68-536">不要在開發或測試環境中使用生產環境祕密。</span><span class="sxs-lookup"><span data-stu-id="03d68-536">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="03d68-537">請在專案外部指定祕密，以防止其意外認可至開放原始碼存放庫。</span><span class="sxs-lookup"><span data-stu-id="03d68-537">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="03d68-538">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="03d68-538">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="03d68-539"><xref:security/app-secrets>：包含使用環境變數來儲存敏感性資料的建議。</span><span class="sxs-lookup"><span data-stu-id="03d68-539"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="03d68-540">秘密管理員工具會使用檔案設定提供者，將使用者秘密儲存在本機系統上的 JSON 檔案中。</span><span class="sxs-lookup"><span data-stu-id="03d68-540">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="03d68-541">此主題稍後將說明「檔案設定提供者」。</span><span class="sxs-lookup"><span data-stu-id="03d68-541">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="03d68-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可安全地儲存 ASP.NET Core 應用程式的應用程式祕密。</span><span class="sxs-lookup"><span data-stu-id="03d68-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="03d68-543">如需詳細資訊，請參閱<xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="03d68-543">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="03d68-544">階層式設定資料</span><span class="sxs-lookup"><span data-stu-id="03d68-544">Hierarchical configuration data</span></span>

<span data-ttu-id="03d68-545">設定 API 可在設定機碼中使用分隔符號來壓平合併階層式資料，以管理階層式設定資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-545">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="03d68-546">在下列 JSON 檔案中，兩個區段的結構式階層中有四個機碼存在：</span><span class="sxs-lookup"><span data-stu-id="03d68-546">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="03d68-547">當該檔案被讀入設定之後，會建立唯一機碼來維護設定來源的原始階層式資料結構。</span><span class="sxs-lookup"><span data-stu-id="03d68-547">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="03d68-548">系統會使用冒號 (`:`) 將區段與機碼壓平合併以維護原始結構：</span><span class="sxs-lookup"><span data-stu-id="03d68-548">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="03d68-549">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-549">section0:key0</span></span>
* <span data-ttu-id="03d68-550">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-550">section0:key1</span></span>
* <span data-ttu-id="03d68-551">section1:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-551">section1:key0</span></span>
* <span data-ttu-id="03d68-552">section1:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-552">section1:key1</span></span>

<span data-ttu-id="03d68-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 與 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用來在設定資料中隔離區段與區段的子系。</span><span class="sxs-lookup"><span data-stu-id="03d68-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="03d68-554">[GetSection,、 GetChildren 與 Exists](#getsection-getchildren-and-exists) 中說明這些方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-554">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="03d68-555">慣例</span><span class="sxs-lookup"><span data-stu-id="03d68-555">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="03d68-556">來源和提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-556">Sources and providers</span></span>

<span data-ttu-id="03d68-557">在應用程式啟動時，會依照設定來源的設定提供者的指定順序讀入設定來源。</span><span class="sxs-lookup"><span data-stu-id="03d68-557">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="03d68-558">實作變更偵測的組態提供者能夠在基礎設定變更時重新載入組態。</span><span class="sxs-lookup"><span data-stu-id="03d68-558">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="03d68-559">例如，檔案組態提供者 (將於本主題稍後討論) 和 [Azure Key Vault 組態提供者](xref:security/key-vault-configuration)均會實作變更偵測。</span><span class="sxs-lookup"><span data-stu-id="03d68-559">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="03d68-560">您可以在應用程式的[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中找到 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="03d68-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="03d68-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可以插入至 Razor 頁面 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> ，以取得類別的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="03d68-562">在下列範例中， `_config` 會使用欄位來存取設定值：</span><span class="sxs-lookup"><span data-stu-id="03d68-562">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="03d68-563">設定提供者無法使用 DI，因為當它們由主機設定時，它無法使用。</span><span class="sxs-lookup"><span data-stu-id="03d68-563">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="03d68-564">索引鍵</span><span class="sxs-lookup"><span data-stu-id="03d68-564">Keys</span></span>

<span data-ttu-id="03d68-565">設定機碼會採用下列慣例：</span><span class="sxs-lookup"><span data-stu-id="03d68-565">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="03d68-566">機碼區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="03d68-566">Keys are case-insensitive.</span></span> <span data-ttu-id="03d68-567">例如，`ConnectionString` 與 `connectionstring` 會被視為相等的機碼。</span><span class="sxs-lookup"><span data-stu-id="03d68-567">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="03d68-568">若相同機碼的值是由相同或不同設定提供者設定，在機碼上設定的最後一個值是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-568">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="03d68-569">如需重複的 JSON 金鑰的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/extensions/issues/2381)。</span><span class="sxs-lookup"><span data-stu-id="03d68-569">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="03d68-570">階層式機碼</span><span class="sxs-lookup"><span data-stu-id="03d68-570">Hierarchical keys</span></span>
  * <span data-ttu-id="03d68-571">在設定 API 內，冒號分隔字元 (`:`) 可在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="03d68-571">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="03d68-572">在環境變數中，冒號分隔字元可能無法在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="03d68-572">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="03d68-573">所有平台都支援雙底線 (`__`)，且會自動轉換為冒號。</span><span class="sxs-lookup"><span data-stu-id="03d68-573">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="03d68-574">在 Azure Key Vault 中，階層式機碼使用 `--` (兩個破折號) 來做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="03d68-574">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="03d68-575">撰寫程式碼，以在將秘密載入至應用程式的設定時，以冒號取代虛線。</span><span class="sxs-lookup"><span data-stu-id="03d68-575">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="03d68-576"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支援在設定機碼中使用陣列索引將陣列繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="03d68-576">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03d68-577">[將陣列繫結到類別](#bind-an-array-to-a-class)一節說明陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="03d68-577">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="03d68-578">值</span><span class="sxs-lookup"><span data-stu-id="03d68-578">Values</span></span>

<span data-ttu-id="03d68-579">設定值會採用下列慣例：</span><span class="sxs-lookup"><span data-stu-id="03d68-579">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="03d68-580">值是字串。</span><span class="sxs-lookup"><span data-stu-id="03d68-580">Values are strings.</span></span>
* <span data-ttu-id="03d68-581">Null 值無法存放在設定中或繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="03d68-581">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="03d68-582">提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-582">Providers</span></span>

<span data-ttu-id="03d68-583">下表顯示可供 ASP.NET Core 應用程式使用的設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-583">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="03d68-584">提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-584">Provider</span></span> | <span data-ttu-id="03d68-585">從&hellip;提供設定</span><span class="sxs-lookup"><span data-stu-id="03d68-585">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="03d68-586">[Azure Key Vault 設定提供者](xref:security/key-vault-configuration) (*安全性* 主題)</span><span class="sxs-lookup"><span data-stu-id="03d68-586">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="03d68-587">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="03d68-587">Azure Key Vault</span></span> |
| <span data-ttu-id="03d68-588">[Azure 應用程式組態提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure 文件)</span><span class="sxs-lookup"><span data-stu-id="03d68-588">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="03d68-589">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="03d68-589">Azure App Configuration</span></span> |
| [<span data-ttu-id="03d68-590">命令列設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-590">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="03d68-591">命令列參數</span><span class="sxs-lookup"><span data-stu-id="03d68-591">Command-line parameters</span></span> |
| [<span data-ttu-id="03d68-592">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-592">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="03d68-593">自訂來源</span><span class="sxs-lookup"><span data-stu-id="03d68-593">Custom source</span></span> |
| [<span data-ttu-id="03d68-594">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-594">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="03d68-595">環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-595">Environment variables</span></span> |
| [<span data-ttu-id="03d68-596">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-596">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="03d68-597">檔案 (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="03d68-597">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="03d68-598">每個檔案機碼的設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-598">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="03d68-599">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-599">Directory files</span></span> |
| [<span data-ttu-id="03d68-600">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-600">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="03d68-601">記憶體內集合</span><span class="sxs-lookup"><span data-stu-id="03d68-601">In-memory collections</span></span> |
| <span data-ttu-id="03d68-602"> (*安全性* 主題的 [使用者密碼](xref:security/app-secrets)) </span><span class="sxs-lookup"><span data-stu-id="03d68-602">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="03d68-603">使用者設定檔目錄中的檔案</span><span class="sxs-lookup"><span data-stu-id="03d68-603">File in the user profile directory</span></span> |

<span data-ttu-id="03d68-604">在啟動時，會依照設定來源的設定提供者的指定順序讀入設定來源。</span><span class="sxs-lookup"><span data-stu-id="03d68-604">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="03d68-605">本主題所描述的設定提供者會依字母順序描述，而不是依照程式碼排列順序。</span><span class="sxs-lookup"><span data-stu-id="03d68-605">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="03d68-606">在程式碼中訂購設定提供者，以符合應用程式所需之基礎設定來源的優先順序。</span><span class="sxs-lookup"><span data-stu-id="03d68-606">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="03d68-607">典型的設定提供者順序是：</span><span class="sxs-lookup"><span data-stu-id="03d68-607">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="03d68-608">檔案 (*appsettings.json* ， *appsettings. {環境} json*，其中 `{Environment}` 是應用程式目前的裝載環境) </span><span class="sxs-lookup"><span data-stu-id="03d68-608">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="03d68-609">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="03d68-609">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="03d68-610">僅)  (開發環境的[使用者秘密](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="03d68-610">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="03d68-611">環境變數</span><span class="sxs-lookup"><span data-stu-id="03d68-611">Environment variables</span></span>
1. <span data-ttu-id="03d68-612">命令列引數</span><span class="sxs-lookup"><span data-stu-id="03d68-612">Command-line arguments</span></span>

<span data-ttu-id="03d68-613">將命令列組態提供者放在提供者序列結尾是常見做法，因為這樣可以讓命令列引數覆寫由其他提供者所設定的組態。</span><span class="sxs-lookup"><span data-stu-id="03d68-613">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="03d68-614">當使用初始化新的主機建立器時，會使用上述的提供者序列 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-614">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03d68-615">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="03d68-615">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="03d68-616">使用 UseConfiguration 設定主機建立器</span><span class="sxs-lookup"><span data-stu-id="03d68-616">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="03d68-617">若要設定主機建立器，請使用該組態在主機建立器上呼叫 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="03d68-617">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="03d68-618">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="03d68-618">ConfigureAppConfiguration</span></span>

<span data-ttu-id="03d68-619">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定提供者，以及由 `CreateDefaultBuilder` 自動新增的設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-619">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="03d68-620">使用命令列引數覆寫先前的組態</span><span class="sxs-lookup"><span data-stu-id="03d68-620">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="03d68-621">若要提供可使用命令列引數覆寫的應用程式組態，請最後呼叫 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="03d68-621">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="03d68-622">移除 >createdefaultbuilder 新增的提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-622">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="03d68-623">若要移除新增的提供者 `CreateDefaultBuilder` ，請在[IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)上呼叫[Clear](/dotnet/api/system.collections.generic.icollection-1.clear) first：</span><span class="sxs-lookup"><span data-stu-id="03d68-623">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="03d68-624">在應用程式啟動期間使用組態</span><span class="sxs-lookup"><span data-stu-id="03d68-624">Consume configuration during app startup</span></span>

<span data-ttu-id="03d68-625">應用程式啟動期間，可以使用 `ConfigureAppConfiguration` 中為應用程式提供的組態，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="03d68-625">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="03d68-626">如需詳細資訊，請參閱[在啟動期間存取組態](#access-configuration-during-startup)一節。</span><span class="sxs-lookup"><span data-stu-id="03d68-626">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="03d68-627">命令列設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-627">Command-line Configuration Provider</span></span>

<span data-ttu-id="03d68-628"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 會在執行階段從命令列引數機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-628">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="03d68-629">為了啟用命令列設定，<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 延伸模組方法會在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫。</span><span class="sxs-lookup"><span data-stu-id="03d68-629">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03d68-630">呼叫 `CreateDefaultBuilder(string [])` 時，會自動呼叫 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="03d68-630">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="03d68-631">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="03d68-631">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03d68-632">`CreateDefaultBuilder` 也會載入：</span><span class="sxs-lookup"><span data-stu-id="03d68-632">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="03d68-633">自和 appsettings 的選擇性設定 *appsettings.json* *。環境}. json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-633">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="03d68-634">開發環境中的[使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03d68-634">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03d68-635">環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-635">Environment variables.</span></span>

<span data-ttu-id="03d68-636">`CreateDefaultBuilder` 會最後才新增命令列設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-636">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="03d68-637">在執行階段傳遞的命令列引數會覆寫由其它提供者所設定的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-637">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="03d68-638">`CreateDefaultBuilder` 會在建構主機時執行作業。</span><span class="sxs-lookup"><span data-stu-id="03d68-638">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="03d68-639">因此，由`CreateDefaultBuilder` 啟用的命令列設定可以影響主機的設定方式。</span><span class="sxs-lookup"><span data-stu-id="03d68-639">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="03d68-640">針對以 ASP.NET Core 範本為基礎的應用程式，`AddCommandLine` 已由 `CreateDefaultBuilder` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="03d68-640">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03d68-641">若要新增其他組態提供者並維持能夠使用命令列引數覆寫這些提供者組態的能力，請在 `ConfigureAppConfiguration` 中呼叫應用程式的其他提供者，且最後呼叫 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="03d68-641">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="03d68-642">**範例**</span><span class="sxs-lookup"><span data-stu-id="03d68-642">**Example**</span></span>

<span data-ttu-id="03d68-643">範例應用程式利用靜態方便方法 `CreateDefaultBuilder` 的優勢來建置主機，這包括對 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="03d68-643">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="03d68-644">從專案目錄中開啟命令提示字元。</span><span class="sxs-lookup"><span data-stu-id="03d68-644">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="03d68-645">提供命令列引數給 `dotnet run` 命令 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="03d68-645">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="03d68-646">在應用程式執行之後，開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-646">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="03d68-647">觀察輸出是否包含提供給 `dotnet run` 之設定命令列引數的機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-647">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="03d68-648">引數</span><span class="sxs-lookup"><span data-stu-id="03d68-648">Arguments</span></span>

<span data-ttu-id="03d68-649">當值後面接著空格時，值必須接著等號 (`=`)，或機碼必須有前置詞 (`--` 或 `/`)。</span><span class="sxs-lookup"><span data-stu-id="03d68-649">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="03d68-650">如果使用等號 (例如 `CommandLineKey=`)，則不需要此值。</span><span class="sxs-lookup"><span data-stu-id="03d68-650">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="03d68-651">索引鍵前置字元</span><span class="sxs-lookup"><span data-stu-id="03d68-651">Key prefix</span></span>               | <span data-ttu-id="03d68-652">範例</span><span class="sxs-lookup"><span data-stu-id="03d68-652">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="03d68-653">沒有前置字元</span><span class="sxs-lookup"><span data-stu-id="03d68-653">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="03d68-654">雙虛線 (`--`)</span><span class="sxs-lookup"><span data-stu-id="03d68-654">Two dashes (`--`)</span></span>        | <span data-ttu-id="03d68-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="03d68-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="03d68-656">正斜線 (`/`)</span><span class="sxs-lookup"><span data-stu-id="03d68-656">Forward slash (`/`)</span></span>      | <span data-ttu-id="03d68-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="03d68-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="03d68-658">在相同的命令中，請不要混合使用等號搭配使用空格之機碼值組的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-658">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="03d68-659">範例命令：</span><span class="sxs-lookup"><span data-stu-id="03d68-659">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="03d68-660">切換對應</span><span class="sxs-lookup"><span data-stu-id="03d68-660">Switch mappings</span></span>

<span data-ttu-id="03d68-661">參數對應允許索引鍵名稱取代邏輯。</span><span class="sxs-lookup"><span data-stu-id="03d68-661">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="03d68-662">使用手動建立設定時 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> ，請提供方法的切換替代字典 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 。</span><span class="sxs-lookup"><span data-stu-id="03d68-662">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="03d68-663">使用切換對應字典時，會檢查字典中是否有任何索引鍵符合命令列引數所提供的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="03d68-663">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="03d68-664">如果在字典中找到命令列索引鍵，則會傳回字典值 (索引鍵取代) 以在應用程式的設定中設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-664">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="03d68-665">所有前面加上單虛線 (`-`) 的命令列索引鍵都需要切換對應。</span><span class="sxs-lookup"><span data-stu-id="03d68-665">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="03d68-666">切換對應字典索引鍵規則：</span><span class="sxs-lookup"><span data-stu-id="03d68-666">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="03d68-667">切換必須以單虛線 (`-`) 或雙虛線 (`--`) 開頭。</span><span class="sxs-lookup"><span data-stu-id="03d68-667">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="03d68-668">切換對應字典不能包含重複索引鍵。</span><span class="sxs-lookup"><span data-stu-id="03d68-668">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="03d68-669">建立切換對應字典。</span><span class="sxs-lookup"><span data-stu-id="03d68-669">Create a switch mappings dictionary.</span></span> <span data-ttu-id="03d68-670">下列範例會建立兩個切換對應：</span><span class="sxs-lookup"><span data-stu-id="03d68-670">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="03d68-671">建立主機時，請使用切換對應字典呼叫 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="03d68-671">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="03d68-672">針對使用切換對應的應用程式，呼叫 `CreateDefaultBuilder` 不應傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-672">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="03d68-673">`CreateDefaultBuilder` 方法的 `AddCommandLine` 呼叫不包括對應的切換，且沒有任何方式可以將切換對應字典傳遞給 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="03d68-673">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03d68-674">解決方案並非將引數傳遞給 `CreateDefaultBuilder`，而是允許 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法同時處理引數與切換對應字典。</span><span class="sxs-lookup"><span data-stu-id="03d68-674">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="03d68-675">建立切換對應字典之後，它會包含下表中所示的資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-675">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="03d68-676">Key</span><span class="sxs-lookup"><span data-stu-id="03d68-676">Key</span></span>       | <span data-ttu-id="03d68-677">值</span><span class="sxs-lookup"><span data-stu-id="03d68-677">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="03d68-678">若啟動應用程式時使用切換對應機碼，設定會接收由字典提供之機碼上的設定值：</span><span class="sxs-lookup"><span data-stu-id="03d68-678">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="03d68-679">執行上述命令之後，設定包含下表中顯示的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-679">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="03d68-680">Key</span><span class="sxs-lookup"><span data-stu-id="03d68-680">Key</span></span>               | <span data-ttu-id="03d68-681">值</span><span class="sxs-lookup"><span data-stu-id="03d68-681">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="03d68-682">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-682">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="03d68-683"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 會在執行階段從環境變數機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-683">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="03d68-684">若要啟用環境變數設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-684">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="03d68-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) 允許在 Azure 入口網站中設定可使用環境變數設定提供者覆寫應用程式設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="03d68-686">如需詳細資訊，請參閱 [Azure App：使用 Azure 入口網站覆寫應用程式設定](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="03d68-686">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="03d68-687">使用 [Web 主機](xref:fundamentals/host/web-host)初始化新的主機建立器並呼叫 `CreateDefaultBuilder` 時，可使用 `AddEnvironmentVariables` 為[主機組態](#host-versus-app-configuration)載入字首為 `ASPNETCORE_` 的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-687">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="03d68-688">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="03d68-688">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03d68-689">`CreateDefaultBuilder` 也會載入：</span><span class="sxs-lookup"><span data-stu-id="03d68-689">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="03d68-690">來自無首碼之環境變數的應用程式組態 (在未提供首碼的情況下呼叫 `AddEnvironmentVariables`)。</span><span class="sxs-lookup"><span data-stu-id="03d68-690">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="03d68-691">自和 appsettings 的選擇性設定 *appsettings.json* *。環境}. json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-691">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="03d68-692">開發環境中的[使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03d68-692">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03d68-693">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-693">Command-line arguments.</span></span>

<span data-ttu-id="03d68-694">從使用者祕密與 *appsettings* 檔案建立設定之後，會呼叫「環境變數設定提供者」。</span><span class="sxs-lookup"><span data-stu-id="03d68-694">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="03d68-695">在此位置呼叫提供者可讓系統在執行階段讀取環境變數，以覆寫由使用者祕密與 *appsettings* 檔案所設定的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-695">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="03d68-696">若要從其他環境變數提供應用程式設定，請在中呼叫應用程式的其他提供者， `ConfigureAppConfiguration` 並使用前置詞來呼叫 `AddEnvironmentVariables` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-696">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="03d68-697">呼叫 `AddEnvironmentVariables` last 以允許具有指定前置詞的環境變數覆寫其他提供者的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-697">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="03d68-698">**範例**</span><span class="sxs-lookup"><span data-stu-id="03d68-698">**Example**</span></span>

<span data-ttu-id="03d68-699">範例應用程式利用靜態方便方法 `CreateDefaultBuilder` 的優勢來建置主機，這包括對 `AddEnvironmentVariables` 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="03d68-699">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="03d68-700">執行範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-700">Run the sample app.</span></span> <span data-ttu-id="03d68-701">開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-701">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="03d68-702">觀察輸出是否包含環境變數 `ENVIRONMENT` 的機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-702">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="03d68-703">值反映應用程式執行所在環境，在本機執行時，通常是 `Development`。</span><span class="sxs-lookup"><span data-stu-id="03d68-703">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="03d68-704">為縮短由應用程式轉譯的環境變數清單，應用程式會篩選環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-704">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="03d68-705">請參閱範例應用程式的 *Pages/Index.cshtml.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-705">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="03d68-706">若要將所有可用的環境變數公開給應用程式，請 `FilteredConfiguration` 將 *Pages/Index. .cs* 變更為下列內容：</span><span class="sxs-lookup"><span data-stu-id="03d68-706">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="03d68-707">首碼</span><span class="sxs-lookup"><span data-stu-id="03d68-707">Prefixes</span></span>

<span data-ttu-id="03d68-708">在提供方法的前置詞時，會篩選載入至應用程式設定的環境變數 `AddEnvironmentVariables` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-708">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="03d68-709">例如，若要篩選前置詞為 `CUSTOM_` 的環境變數，請提供前置詞給設定提供者：</span><span class="sxs-lookup"><span data-stu-id="03d68-709">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="03d68-710">建立設定機碼值組時，會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="03d68-710">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="03d68-711">建立主機建立器時，主機組態由環境變數提供。</span><span class="sxs-lookup"><span data-stu-id="03d68-711">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="03d68-712">如需這些環境變數所用前置詞的詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="03d68-712">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03d68-713">**連接字串前置詞**</span><span class="sxs-lookup"><span data-stu-id="03d68-713">**Connection string prefixes**</span></span>

<span data-ttu-id="03d68-714">設定 API 四個連接字串環境變數的特殊處理規則，這些這些環境變數牽涉到針對應用程式環境設定 Azure 連接字串。</span><span class="sxs-lookup"><span data-stu-id="03d68-714">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="03d68-715">若將前置詞提供給 `AddEnvironmentVariables`具有下表顯示之前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-715">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="03d68-716">連接字串前置詞</span><span class="sxs-lookup"><span data-stu-id="03d68-716">Connection string prefix</span></span> | <span data-ttu-id="03d68-717">提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-717">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="03d68-718">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-718">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="03d68-719">MySQL</span><span class="sxs-lookup"><span data-stu-id="03d68-719">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="03d68-720">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="03d68-720">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="03d68-721">SQL Server</span><span class="sxs-lookup"><span data-stu-id="03d68-721">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="03d68-722">當探索到具有下表所顯示之任何四個前置詞的環境變數並將其載入到設定中時：</span><span class="sxs-lookup"><span data-stu-id="03d68-722">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="03d68-723">會透過移除環境變數前置詞並新增設定機碼區段 (`ConnectionStrings`) 來移除具有下表顯示之前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-723">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="03d68-724">會建立新的設定機碼值組以代表資料庫連線提供者 (`CUSTOMCONNSTR_` 除外，這沒有所述提供者)。</span><span class="sxs-lookup"><span data-stu-id="03d68-724">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="03d68-725">環境變數機碼</span><span class="sxs-lookup"><span data-stu-id="03d68-725">Environment variable key</span></span> | <span data-ttu-id="03d68-726">已轉換的設定機碼</span><span class="sxs-lookup"><span data-stu-id="03d68-726">Converted configuration key</span></span> | <span data-ttu-id="03d68-727">提供者設定項目</span><span class="sxs-lookup"><span data-stu-id="03d68-727">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-728">設定項目未建立。</span><span class="sxs-lookup"><span data-stu-id="03d68-728">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-729">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="03d68-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03d68-730">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="03d68-730">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-731">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="03d68-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03d68-732">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="03d68-732">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="03d68-733">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="03d68-733">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03d68-734">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="03d68-734">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="03d68-735">**範例**</span><span class="sxs-lookup"><span data-stu-id="03d68-735">**Example**</span></span>

<span data-ttu-id="03d68-736">在伺服器上建立自訂連接字串環境變數：</span><span class="sxs-lookup"><span data-stu-id="03d68-736">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="03d68-737">名稱：`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="03d68-737">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="03d68-738">值：`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="03d68-738">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="03d68-739">如果 `IConfiguration` 插入並指派給名為的欄位 `_config` ，請閱讀下列值：</span><span class="sxs-lookup"><span data-stu-id="03d68-739">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="03d68-740">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-740">File Configuration Provider</span></span>

<span data-ttu-id="03d68-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是用於從檔案系統載入設定的基底類別。</span><span class="sxs-lookup"><span data-stu-id="03d68-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="03d68-742">下列設定提供者專用於特定檔案類型：</span><span class="sxs-lookup"><span data-stu-id="03d68-742">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="03d68-743">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-743">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="03d68-744">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-744">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="03d68-745">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-745">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="03d68-746">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-746">INI Configuration Provider</span></span>

<span data-ttu-id="03d68-747"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 會在執行階段從 INI 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-747">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="03d68-748">若要啟用 INI 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-748">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03d68-749">冒號可用來做為 INI 檔案設定中的區段分隔符號。</span><span class="sxs-lookup"><span data-stu-id="03d68-749">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="03d68-750">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="03d68-750">Overloads permit specifying:</span></span>

* <span data-ttu-id="03d68-751">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="03d68-751">Whether the file is optional.</span></span>
* <span data-ttu-id="03d68-752">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-752">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="03d68-753"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-753">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="03d68-754">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-754">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="03d68-755">INI 設定檔的一般範例：</span><span class="sxs-lookup"><span data-stu-id="03d68-755">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="03d68-756">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-756">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03d68-757">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-757">section0:key0</span></span>
* <span data-ttu-id="03d68-758">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-758">section0:key1</span></span>
* <span data-ttu-id="03d68-759">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="03d68-759">section1:subsection:key</span></span>
* <span data-ttu-id="03d68-760">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="03d68-760">section2:subsection0:key</span></span>
* <span data-ttu-id="03d68-761">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="03d68-761">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="03d68-762">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-762">JSON Configuration Provider</span></span>

<span data-ttu-id="03d68-763"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 會在執行階段從 JSON 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-763">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="03d68-764">若要啟用 JSON 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-764">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03d68-765">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="03d68-765">Overloads permit specifying:</span></span>

* <span data-ttu-id="03d68-766">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="03d68-766">Whether the file is optional.</span></span>
* <span data-ttu-id="03d68-767">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-767">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="03d68-768"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-768">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="03d68-769">`AddJsonFile` 使用初始化新的主機建立器時，會自動呼叫兩次 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-769">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03d68-770">會呼叫此方法以從下列位置載入設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-770">The method is called to load configuration from:</span></span>

* <span data-ttu-id="03d68-771">*appsettings.json*：先讀取此檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-771">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="03d68-772">檔案的環境版本可以覆寫檔案所提供的值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-772">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="03d68-773">*appsettings。{環境}. json*：檔案的環境版本是根據 [IHostingEnvironment EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)載入。</span><span class="sxs-lookup"><span data-stu-id="03d68-773">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="03d68-774">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="03d68-774">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03d68-775">`CreateDefaultBuilder` 也會載入：</span><span class="sxs-lookup"><span data-stu-id="03d68-775">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="03d68-776">環境變數。</span><span class="sxs-lookup"><span data-stu-id="03d68-776">Environment variables.</span></span>
* <span data-ttu-id="03d68-777">開發環境中的[使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03d68-777">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03d68-778">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="03d68-778">Command-line arguments.</span></span>

<span data-ttu-id="03d68-779">會先建立 JSON 設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-779">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="03d68-780">因此，使用者祕密、環境變數與命令列引數會覆寫由 *appsettings* 檔案設定的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-780">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="03d68-781">`ConfigureAppConfiguration`建立主機以針對和 appsettings 以外的檔案指定應用程式的設定時，請呼叫 *appsettings.json* *。 {環境}. json*：</span><span class="sxs-lookup"><span data-stu-id="03d68-781">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="03d68-782">**範例**</span><span class="sxs-lookup"><span data-stu-id="03d68-782">**Example**</span></span>

<span data-ttu-id="03d68-783">範例應用程式會利用靜態的便利性方法 `CreateDefaultBuilder` 來建立主機，其中包含兩個對的呼叫 `AddJsonFile` ：</span><span class="sxs-lookup"><span data-stu-id="03d68-783">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="03d68-784">從下列載入設定的第一個呼叫 `AddJsonFile` *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="03d68-784">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="03d68-785">從 appsettings 載入設定的第二個呼叫 `AddJsonFile` *。 {環境}. json*。</span><span class="sxs-lookup"><span data-stu-id="03d68-785">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="03d68-786">針對範例應用程式中的 *appsettings.Development.js* ，會載入下列檔案：</span><span class="sxs-lookup"><span data-stu-id="03d68-786">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="03d68-787">執行範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-787">Run the sample app.</span></span> <span data-ttu-id="03d68-788">開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-788">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="03d68-789">輸出會根據應用程式的環境，包含設定的機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-789">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="03d68-790">金鑰的記錄層級 `Logging:LogLevel:Default` 是在 `Debug` 開發環境中執行應用程式時。</span><span class="sxs-lookup"><span data-stu-id="03d68-790">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="03d68-791">在生產環境中再次執行範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="03d68-791">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="03d68-792">開啟檔案的 *屬性/launchSettings.js* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-792">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="03d68-793">在 `ConfigurationSample` 設定檔中，將環境變數的值變更 `ASPNETCORE_ENVIRONMENT` 為 `Production` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-793">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="03d68-794">使用命令介面儲存檔案，並 `dotnet run` 在中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="03d68-794">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="03d68-795">*appsettings.Development.js* 中的設定不會再覆寫中的設定 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="03d68-795">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="03d68-796">索引鍵的記錄層級 `Logging:LogLevel:Default` 為 `Warning` 。</span><span class="sxs-lookup"><span data-stu-id="03d68-796">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="03d68-797">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-797">XML Configuration Provider</span></span>

<span data-ttu-id="03d68-798"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 會在執行階段從 XML 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-798">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="03d68-799">若要啟用 XML 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-799">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03d68-800">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="03d68-800">Overloads permit specifying:</span></span>

* <span data-ttu-id="03d68-801">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="03d68-801">Whether the file is optional.</span></span>
* <span data-ttu-id="03d68-802">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-802">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="03d68-803"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-803">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="03d68-804">建立設定機碼值組時，會忽略設定檔案的根節點。</span><span class="sxs-lookup"><span data-stu-id="03d68-804">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="03d68-805">請勿在檔案中指定文件類型定義 (DTD) 或命名空間。</span><span class="sxs-lookup"><span data-stu-id="03d68-805">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="03d68-806">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-806">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="03d68-807">XML 設定檔可以針對重複的區段使用相異元素名稱：</span><span class="sxs-lookup"><span data-stu-id="03d68-807">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="03d68-808">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03d68-809">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-809">section0:key0</span></span>
* <span data-ttu-id="03d68-810">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-810">section0:key1</span></span>
* <span data-ttu-id="03d68-811">section1:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-811">section1:key0</span></span>
* <span data-ttu-id="03d68-812">section1:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-812">section1:key1</span></span>

<span data-ttu-id="03d68-813">若 `name` 屬性是用來區別元素，則可以重複那些使用相同元素名稱的元素：</span><span class="sxs-lookup"><span data-stu-id="03d68-813">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="03d68-814">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03d68-815">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-815">section:section0:key:key0</span></span>
* <span data-ttu-id="03d68-816">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-816">section:section0:key:key1</span></span>
* <span data-ttu-id="03d68-817">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-817">section:section1:key:key0</span></span>
* <span data-ttu-id="03d68-818">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-818">section:section1:key:key1</span></span>

<span data-ttu-id="03d68-819">屬性可用來提供值：</span><span class="sxs-lookup"><span data-stu-id="03d68-819">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="03d68-820">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-820">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03d68-821">key:attribute</span><span class="sxs-lookup"><span data-stu-id="03d68-821">key:attribute</span></span>
* <span data-ttu-id="03d68-822">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="03d68-822">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="03d68-823">每個檔案機碼設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-823">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="03d68-824"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目錄的檔案做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-824">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="03d68-825">機碼是檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="03d68-825">The key is the file name.</span></span> <span data-ttu-id="03d68-826">值包含檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="03d68-826">The value contains the file's contents.</span></span> <span data-ttu-id="03d68-827">每個檔案機碼設定提供者是在 Docker 主機案例中使用。</span><span class="sxs-lookup"><span data-stu-id="03d68-827">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="03d68-828">若要啟用每個檔案機碼設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-828">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="03d68-829">檔案的 `directoryPath` 必須是絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="03d68-829">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="03d68-830">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="03d68-830">Overloads permit specifying:</span></span>

* <span data-ttu-id="03d68-831">設定來源的 `Action<KeyPerFileConfigurationSource>` 委派。</span><span class="sxs-lookup"><span data-stu-id="03d68-831">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="03d68-832">目錄是否為選擇性與目錄的路徑。</span><span class="sxs-lookup"><span data-stu-id="03d68-832">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="03d68-833">雙底線 (`__`) 是做為檔案名稱中的設定金鑰分隔符號使用。</span><span class="sxs-lookup"><span data-stu-id="03d68-833">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="03d68-834">例如，檔案名稱 `Logging__LogLevel__System` 會產生設定金鑰 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="03d68-834">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="03d68-835">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="03d68-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="03d68-836">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-836">Memory Configuration Provider</span></span>

<span data-ttu-id="03d68-837"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用記憶體內集合做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="03d68-837">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="03d68-838">若要啟用記憶體內集合設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="03d68-838">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03d68-839">設定提供者可以使用 `IEnumerable<KeyValuePair<String,String>>` 來初始化。</span><span class="sxs-lookup"><span data-stu-id="03d68-839">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="03d68-840">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="03d68-840">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="03d68-841">下列範例會建立組態字典：</span><span class="sxs-lookup"><span data-stu-id="03d68-841">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="03d68-842">字典會與 `AddInMemoryCollection` 的呼叫搭配使用以提供組態：</span><span class="sxs-lookup"><span data-stu-id="03d68-842">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="03d68-843">GetValue</span><span class="sxs-lookup"><span data-stu-id="03d68-843">GetValue</span></span>

<span data-ttu-id="03d68-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 從具有指定索引鍵的設定中解壓縮單一值，並將其轉換為指定的 noncollection 類型。</span><span class="sxs-lookup"><span data-stu-id="03d68-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="03d68-845">多載接受預設值。</span><span class="sxs-lookup"><span data-stu-id="03d68-845">An overload accepts a default value.</span></span>

<span data-ttu-id="03d68-846">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="03d68-846">The following example:</span></span>

* <span data-ttu-id="03d68-847">從具有機碼 `NumberKey` 的組態擷取字串值。</span><span class="sxs-lookup"><span data-stu-id="03d68-847">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="03d68-848">若在組態機碼中找不到 `NumberKey`，則會使用預設值 `99`。</span><span class="sxs-lookup"><span data-stu-id="03d68-848">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="03d68-849">鍵入值為 `int`。</span><span class="sxs-lookup"><span data-stu-id="03d68-849">Types the value as an `int`.</span></span>
* <span data-ttu-id="03d68-850">在 `NumberConfig` 屬性中儲存值供頁面使用。</span><span class="sxs-lookup"><span data-stu-id="03d68-850">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="03d68-851">GetSection、GetChildren 與 Exists</span><span class="sxs-lookup"><span data-stu-id="03d68-851">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="03d68-852">針對下面的範例，請考慮下列 JSON 檔案。</span><span class="sxs-lookup"><span data-stu-id="03d68-852">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="03d68-853">在兩個區段中找到四個機碼，其中一個包括子區段組：</span><span class="sxs-lookup"><span data-stu-id="03d68-853">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="03d68-854">當檔案被讀入到設定之後，會建立下列唯一階層式機碼以存放設定值：</span><span class="sxs-lookup"><span data-stu-id="03d68-854">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="03d68-855">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-855">section0:key0</span></span>
* <span data-ttu-id="03d68-856">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-856">section0:key1</span></span>
* <span data-ttu-id="03d68-857">section1:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-857">section1:key0</span></span>
* <span data-ttu-id="03d68-858">section1:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-858">section1:key1</span></span>
* <span data-ttu-id="03d68-859">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-859">section2:subsection0:key0</span></span>
* <span data-ttu-id="03d68-860">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-860">section2:subsection0:key1</span></span>
* <span data-ttu-id="03d68-861">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="03d68-861">section2:subsection1:key0</span></span>
* <span data-ttu-id="03d68-862">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="03d68-862">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="03d68-863">GetSection</span><span class="sxs-lookup"><span data-stu-id="03d68-863">GetSection</span></span>

<span data-ttu-id="03d68-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 會擷取具有所指定子區段機碼的設定子區段。</span><span class="sxs-lookup"><span data-stu-id="03d68-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="03d68-865">若要傳回 `section1` 中只包含一個機碼值組的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，請呼叫 `GetSection` 並提供區段名稱：</span><span class="sxs-lookup"><span data-stu-id="03d68-865">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="03d68-866">`configSection` 沒有值，只有索引鍵和路徑。</span><span class="sxs-lookup"><span data-stu-id="03d68-866">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="03d68-867">同樣地，若要取得 `section2:subsection0` 中之機碼的值，請呼叫 `GetSection` 並提供區段路徑：</span><span class="sxs-lookup"><span data-stu-id="03d68-867">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="03d68-868">`GetSection` 絕不會傳回 `null`。</span><span class="sxs-lookup"><span data-stu-id="03d68-868">`GetSection` never returns `null`.</span></span> <span data-ttu-id="03d68-869">若找不到相符的區段，會傳回空的 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="03d68-869">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="03d68-870">當 `GetSection` 傳回相符區段時，未填入 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>。</span><span class="sxs-lookup"><span data-stu-id="03d68-870">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="03d68-871">當區段存在時，會傳回 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 與 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>。</span><span class="sxs-lookup"><span data-stu-id="03d68-871">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="03d68-872">GetChildren</span><span class="sxs-lookup"><span data-stu-id="03d68-872">GetChildren</span></span>

<span data-ttu-id="03d68-873">對 `section2` 上之 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 的呼叫會取得包括下列項目的 `IEnumerable<IConfigurationSection>`：</span><span class="sxs-lookup"><span data-stu-id="03d68-873">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="03d68-874">Exists</span><span class="sxs-lookup"><span data-stu-id="03d68-874">Exists</span></span>

<span data-ttu-id="03d68-875">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 來判斷設定區段是否存在：</span><span class="sxs-lookup"><span data-stu-id="03d68-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="03d68-876">以範例資料為例， `sectionExists` 是 `false`，因未設定資料中沒有 `section2:subsection2` 區段。</span><span class="sxs-lookup"><span data-stu-id="03d68-876">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="03d68-877">繫結至物件圖形</span><span class="sxs-lookup"><span data-stu-id="03d68-877">Bind to an object graph</span></span>

<span data-ttu-id="03d68-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以繫結整個 POCO 物件圖形。</span><span class="sxs-lookup"><span data-stu-id="03d68-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="03d68-879">如同系結簡單的物件，只會系結公用讀取/寫入屬性。</span><span class="sxs-lookup"><span data-stu-id="03d68-879">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="03d68-880">範例包含 `TvShow` 模型，其物件圖形包括 `Metadata` 與 `Actors` 類別 (*Models/TvShow.cs*)：</span><span class="sxs-lookup"><span data-stu-id="03d68-880">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="03d68-881">範例應用程式有 *tvshow.xml* 檔案，其中包含設定資料：</span><span class="sxs-lookup"><span data-stu-id="03d68-881">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="03d68-882">設定會被繫結到整個 `TvShow` 物件 (使用 `Bind` 方法)。</span><span class="sxs-lookup"><span data-stu-id="03d68-882">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="03d68-883">已繫結的執行個體會被指派給屬性以用於轉譯：</span><span class="sxs-lookup"><span data-stu-id="03d68-883">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="03d68-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 系結並傳回指定的型別。</span><span class="sxs-lookup"><span data-stu-id="03d68-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="03d68-885">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="03d68-885">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="03d68-886">下列程式碼說明如何搭配 `Get<T>` 上述範例使用：</span><span class="sxs-lookup"><span data-stu-id="03d68-886">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="03d68-887">將陣列繫結到類別</span><span class="sxs-lookup"><span data-stu-id="03d68-887">Bind an array to a class</span></span>

<span data-ttu-id="03d68-888">範例應用程式示範此節中解釋的概念。</span><span class="sxs-lookup"><span data-stu-id="03d68-888">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="03d68-889"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支援在設定機碼中使用陣列索引將陣列繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="03d68-889">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03d68-890">任何公開數位索引鍵區段 (、) 的陣列格式， `:0:` `:1:` &hellip; `:{n}:` 都能夠將陣列系結至 POCO 類別陣列。</span><span class="sxs-lookup"><span data-stu-id="03d68-890">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="03d68-891">繫結是由慣例提供。</span><span class="sxs-lookup"><span data-stu-id="03d68-891">Binding is provided by convention.</span></span> <span data-ttu-id="03d68-892">自訂設定提供者不需要實作陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="03d68-892">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="03d68-893">**記憶體內陣列處理**</span><span class="sxs-lookup"><span data-stu-id="03d68-893">**In-memory array processing**</span></span>

<span data-ttu-id="03d68-894">考慮下表中顯示的設定機碼與值。</span><span class="sxs-lookup"><span data-stu-id="03d68-894">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="03d68-895">Key</span><span class="sxs-lookup"><span data-stu-id="03d68-895">Key</span></span>             | <span data-ttu-id="03d68-896">值</span><span class="sxs-lookup"><span data-stu-id="03d68-896">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="03d68-897">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="03d68-897">array:entries:0</span></span> | <span data-ttu-id="03d68-898">value0</span><span class="sxs-lookup"><span data-stu-id="03d68-898">value0</span></span> |
| <span data-ttu-id="03d68-899">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="03d68-899">array:entries:1</span></span> | <span data-ttu-id="03d68-900">value1</span><span class="sxs-lookup"><span data-stu-id="03d68-900">value1</span></span> |
| <span data-ttu-id="03d68-901">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="03d68-901">array:entries:2</span></span> | <span data-ttu-id="03d68-902">value2</span><span class="sxs-lookup"><span data-stu-id="03d68-902">value2</span></span> |
| <span data-ttu-id="03d68-903">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="03d68-903">array:entries:4</span></span> | <span data-ttu-id="03d68-904">value4</span><span class="sxs-lookup"><span data-stu-id="03d68-904">value4</span></span> |
| <span data-ttu-id="03d68-905">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="03d68-905">array:entries:5</span></span> | <span data-ttu-id="03d68-906">value5</span><span class="sxs-lookup"><span data-stu-id="03d68-906">value5</span></span> |

<span data-ttu-id="03d68-907">這些機碼與值是使用「記憶體設定提供者」在範例應用程式中載入：</span><span class="sxs-lookup"><span data-stu-id="03d68-907">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="03d68-908">陣列會跳過索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-908">The array skips a value for index &num;3.</span></span> <span data-ttu-id="03d68-909">設定繫結程式無法繫結 Null 值或在已繫結的物件中建立 Null 項目，這在示範繫結此陣列的結果到某物件的時候已經很清楚。</span><span class="sxs-lookup"><span data-stu-id="03d68-909">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="03d68-910">在範例應用程式中，POCO 類別可用來存放繫結設定資料：</span><span class="sxs-lookup"><span data-stu-id="03d68-910">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="03d68-911">設定資料已繫結到物件：</span><span class="sxs-lookup"><span data-stu-id="03d68-911">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="03d68-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 也可以使用語法，以產生更精簡的程式碼：</span><span class="sxs-lookup"><span data-stu-id="03d68-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="03d68-913">繫結物件 (`ArrayExample`的執行個體) 會從設定接收陣列資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-913">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="03d68-914">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="03d68-914">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="03d68-915">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="03d68-915">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="03d68-916">0</span><span class="sxs-lookup"><span data-stu-id="03d68-916">0</span></span>                            | <span data-ttu-id="03d68-917">value0</span><span class="sxs-lookup"><span data-stu-id="03d68-917">value0</span></span>                       |
| <span data-ttu-id="03d68-918">1</span><span class="sxs-lookup"><span data-stu-id="03d68-918">1</span></span>                            | <span data-ttu-id="03d68-919">value1</span><span class="sxs-lookup"><span data-stu-id="03d68-919">value1</span></span>                       |
| <span data-ttu-id="03d68-920">2</span><span class="sxs-lookup"><span data-stu-id="03d68-920">2</span></span>                            | <span data-ttu-id="03d68-921">value2</span><span class="sxs-lookup"><span data-stu-id="03d68-921">value2</span></span>                       |
| <span data-ttu-id="03d68-922">3</span><span class="sxs-lookup"><span data-stu-id="03d68-922">3</span></span>                            | <span data-ttu-id="03d68-923">value4</span><span class="sxs-lookup"><span data-stu-id="03d68-923">value4</span></span>                       |
| <span data-ttu-id="03d68-924">4</span><span class="sxs-lookup"><span data-stu-id="03d68-924">4</span></span>                            | <span data-ttu-id="03d68-925">value5</span><span class="sxs-lookup"><span data-stu-id="03d68-925">value5</span></span>                       |

<span data-ttu-id="03d68-926">繫結物件中的索引 &num;3 存放 `array:4` 設定機碼與其 `value4` 的設定資料。</span><span class="sxs-lookup"><span data-stu-id="03d68-926">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="03d68-927">當繫結包含陣列的設定資料時，設定機碼中的陣列索引只會用來列舉設定資料 (當建立物件時)。</span><span class="sxs-lookup"><span data-stu-id="03d68-927">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="03d68-928">設定資料中不能保留 Null 值，當設定機碼中的陣列略過一或多個索引時，不會在繫結物件中建立 Null 值項目。</span><span class="sxs-lookup"><span data-stu-id="03d68-928">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="03d68-929">由會在設定中產生正確機碼值組的任何設定提供者繫結到 `ArrayExample` 執行個體之前，可以提供索引 &num;3 缺少的設定項目。</span><span class="sxs-lookup"><span data-stu-id="03d68-929">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="03d68-930">若範例包含具有缺少之機碼值組的額外 JSON 設定提供者，`ArrayExample.Entries` 會符合完整的設定陣列：</span><span class="sxs-lookup"><span data-stu-id="03d68-930">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="03d68-931">*missing_value.json*：</span><span class="sxs-lookup"><span data-stu-id="03d68-931">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="03d68-932">在 `ConfigureAppConfiguration` 中：</span><span class="sxs-lookup"><span data-stu-id="03d68-932">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="03d68-933">表格中顯示的機碼值組會載入到設定中。</span><span class="sxs-lookup"><span data-stu-id="03d68-933">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="03d68-934">Key</span><span class="sxs-lookup"><span data-stu-id="03d68-934">Key</span></span>             | <span data-ttu-id="03d68-935">值</span><span class="sxs-lookup"><span data-stu-id="03d68-935">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="03d68-936">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="03d68-936">array:entries:3</span></span> | <span data-ttu-id="03d68-937">value3</span><span class="sxs-lookup"><span data-stu-id="03d68-937">value3</span></span> |

<span data-ttu-id="03d68-938">在「JSON 設定提供者」包含索引 &num;3 的項目之後，若繫結 `ArrayExample` 類別執行個體，`ArrayExample.Entries` 陣列會包括這些值：</span><span class="sxs-lookup"><span data-stu-id="03d68-938">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="03d68-939">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="03d68-939">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="03d68-940">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="03d68-940">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="03d68-941">0</span><span class="sxs-lookup"><span data-stu-id="03d68-941">0</span></span>                            | <span data-ttu-id="03d68-942">value0</span><span class="sxs-lookup"><span data-stu-id="03d68-942">value0</span></span>                       |
| <span data-ttu-id="03d68-943">1</span><span class="sxs-lookup"><span data-stu-id="03d68-943">1</span></span>                            | <span data-ttu-id="03d68-944">value1</span><span class="sxs-lookup"><span data-stu-id="03d68-944">value1</span></span>                       |
| <span data-ttu-id="03d68-945">2</span><span class="sxs-lookup"><span data-stu-id="03d68-945">2</span></span>                            | <span data-ttu-id="03d68-946">value2</span><span class="sxs-lookup"><span data-stu-id="03d68-946">value2</span></span>                       |
| <span data-ttu-id="03d68-947">3</span><span class="sxs-lookup"><span data-stu-id="03d68-947">3</span></span>                            | <span data-ttu-id="03d68-948">value3</span><span class="sxs-lookup"><span data-stu-id="03d68-948">value3</span></span>                       |
| <span data-ttu-id="03d68-949">4</span><span class="sxs-lookup"><span data-stu-id="03d68-949">4</span></span>                            | <span data-ttu-id="03d68-950">value4</span><span class="sxs-lookup"><span data-stu-id="03d68-950">value4</span></span>                       |
| <span data-ttu-id="03d68-951">5</span><span class="sxs-lookup"><span data-stu-id="03d68-951">5</span></span>                            | <span data-ttu-id="03d68-952">value5</span><span class="sxs-lookup"><span data-stu-id="03d68-952">value5</span></span>                       |

<span data-ttu-id="03d68-953">**JSON 陣列處理**</span><span class="sxs-lookup"><span data-stu-id="03d68-953">**JSON array processing**</span></span>

<span data-ttu-id="03d68-954">若 JSON 檔案包含陣列，會為具有以零為基礎之區段索引的陣列元素建立設定機碼。</span><span class="sxs-lookup"><span data-stu-id="03d68-954">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="03d68-955">在下列設定檔中，`subsection` 是陣列：</span><span class="sxs-lookup"><span data-stu-id="03d68-955">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="03d68-956">「JSON 設定提供者」會將設定資料讀入到下列機碼值組：</span><span class="sxs-lookup"><span data-stu-id="03d68-956">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="03d68-957">Key</span><span class="sxs-lookup"><span data-stu-id="03d68-957">Key</span></span>                     | <span data-ttu-id="03d68-958">值</span><span class="sxs-lookup"><span data-stu-id="03d68-958">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="03d68-959">json_array:key</span><span class="sxs-lookup"><span data-stu-id="03d68-959">json_array:key</span></span>          | <span data-ttu-id="03d68-960">valueA</span><span class="sxs-lookup"><span data-stu-id="03d68-960">valueA</span></span> |
| <span data-ttu-id="03d68-961">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="03d68-961">json_array:subsection:0</span></span> | <span data-ttu-id="03d68-962">valueB</span><span class="sxs-lookup"><span data-stu-id="03d68-962">valueB</span></span> |
| <span data-ttu-id="03d68-963">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="03d68-963">json_array:subsection:1</span></span> | <span data-ttu-id="03d68-964">valueC</span><span class="sxs-lookup"><span data-stu-id="03d68-964">valueC</span></span> |
| <span data-ttu-id="03d68-965">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="03d68-965">json_array:subsection:2</span></span> | <span data-ttu-id="03d68-966">valueD</span><span class="sxs-lookup"><span data-stu-id="03d68-966">valueD</span></span> |

<span data-ttu-id="03d68-967">在範例應用程式中，下列 POCO 類別可用來繫結設定機碼值組：</span><span class="sxs-lookup"><span data-stu-id="03d68-967">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="03d68-968">在繫結之後，`JsonArrayExample.Key` 會存放值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="03d68-968">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="03d68-969">子區段值會存放在 POCO 陣列屬性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="03d68-969">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="03d68-970">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="03d68-970">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="03d68-971">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="03d68-971">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="03d68-972">0</span><span class="sxs-lookup"><span data-stu-id="03d68-972">0</span></span>                                   | <span data-ttu-id="03d68-973">valueB</span><span class="sxs-lookup"><span data-stu-id="03d68-973">valueB</span></span>                              |
| <span data-ttu-id="03d68-974">1</span><span class="sxs-lookup"><span data-stu-id="03d68-974">1</span></span>                                   | <span data-ttu-id="03d68-975">valueC</span><span class="sxs-lookup"><span data-stu-id="03d68-975">valueC</span></span>                              |
| <span data-ttu-id="03d68-976">2</span><span class="sxs-lookup"><span data-stu-id="03d68-976">2</span></span>                                   | <span data-ttu-id="03d68-977">valueD</span><span class="sxs-lookup"><span data-stu-id="03d68-977">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="03d68-978">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="03d68-978">Custom configuration provider</span></span>

<span data-ttu-id="03d68-979">範例應用程式示範如何建立會使用 [Entity Framework (EF)](/ef/core/) 從資料庫讀取設定機碼值組的基本設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-979">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="03d68-980">提供者具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="03d68-980">The provider has the following characteristics:</span></span>

* <span data-ttu-id="03d68-981">EF 記憶體內資料庫會用於示範用途。</span><span class="sxs-lookup"><span data-stu-id="03d68-981">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="03d68-982">若要使用需要連接字串的資料庫，請實作第二個 `ConfigurationBuilder` 以從另一個設定提供者提供連接字串。</span><span class="sxs-lookup"><span data-stu-id="03d68-982">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="03d68-983">啟動時，該提供者會將資料庫資料表讀入到設定中。</span><span class="sxs-lookup"><span data-stu-id="03d68-983">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="03d68-984">該提供者不會以個別機碼為基礎查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="03d68-984">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="03d68-985">未實作變更時重新載入，因此在應用程式啟動後更新資料庫對應用程式設定沒有影響。</span><span class="sxs-lookup"><span data-stu-id="03d68-985">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="03d68-986">定義 `EFConfigurationValue` 實體來在資料庫中存放設定值。</span><span class="sxs-lookup"><span data-stu-id="03d68-986">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="03d68-987">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-987">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="03d68-988">新增 `EFConfigurationContext` 以存放及存取已設定的值。</span><span class="sxs-lookup"><span data-stu-id="03d68-988">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="03d68-989">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="03d68-990">建立會實作 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的類別。</span><span class="sxs-lookup"><span data-stu-id="03d68-990">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="03d68-991">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="03d68-992">透過繼承自 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 來建立自訂設定提供者。</span><span class="sxs-lookup"><span data-stu-id="03d68-992">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="03d68-993">若資料庫是空的，設定提供者會初始化資料庫。</span><span class="sxs-lookup"><span data-stu-id="03d68-993">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="03d68-994">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="03d68-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="03d68-995">`AddEFConfiguration` 擴充方法允許新增設定來源到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="03d68-995">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="03d68-996">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="03d68-996">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="03d68-997">下列程式碼顯示如何在 *Program.cs* 中使用自訂 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="03d68-997">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="03d68-998">在啟動期間存取組態</span><span class="sxs-lookup"><span data-stu-id="03d68-998">Access configuration during startup</span></span>

<span data-ttu-id="03d68-999">將 `IConfiguration` 插入到 `Startup` 建構函式，以存取 `Startup.ConfigureServices` 中的設定值。</span><span class="sxs-lookup"><span data-stu-id="03d68-999">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="03d68-1000">若要存取 `Startup.Configure` 中的設定，請直接將 `IConfiguration` 插入到方法或從建構函式使用執行個體：</span><span class="sxs-lookup"><span data-stu-id="03d68-1000">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="03d68-1001">如需使用啟動方便方法來存取設定的範例，請參閱[應用程式啟動：方便方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="03d68-1001">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="03d68-1002">存取 Razor 頁面頁面或 MVC 視圖中的設定</span><span class="sxs-lookup"><span data-stu-id="03d68-1002">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="03d68-1003">若要存取 Razor 頁面頁面或 MVC 視圖中的設定設定，請新增 [using](xref:mvc/views/razor#using) 指示詞 ([c # 參考：使用](/dotnet/csharp/language-reference/keywords/using-directive) [Microsoft.Extensions.Configuration 命名空間](xref:Microsoft.Extensions.Configuration) 的指示詞) ，然後插入 <xref:Microsoft.Extensions.Configuration.IConfiguration> 頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="03d68-1003">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="03d68-1004">在 [ Razor 頁面] 頁面中：</span><span class="sxs-lookup"><span data-stu-id="03d68-1004">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="03d68-1005">在 MVC 檢視中：</span><span class="sxs-lookup"><span data-stu-id="03d68-1005">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="03d68-1006">從外部組件新增設定</span><span class="sxs-lookup"><span data-stu-id="03d68-1006">Add configuration from an external assembly</span></span>

<span data-ttu-id="03d68-1007"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 實作允許在啟動時從應用程式 `Startup` 類別外部的外部組件，針對應用程式新增增強功能。</span><span class="sxs-lookup"><span data-stu-id="03d68-1007">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="03d68-1008">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="03d68-1008">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03d68-1009">其他資源</span><span class="sxs-lookup"><span data-stu-id="03d68-1009">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
