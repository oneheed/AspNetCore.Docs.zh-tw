---
title: ASP.NET Core 的設定
author: rick-anderson
description: 了解如何使用組態 API 設定 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536280"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8739b-103">ASP.NET Core 的設定</span><span class="sxs-lookup"><span data-stu-id="8739b-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8739b-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8739b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8739b-105">ASP.NET Core 中的設定是使用一或多個設定 [提供者](#cp)來執行。</span><span class="sxs-lookup"><span data-stu-id="8739b-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8739b-106">設定提供者會使用各種設定來源從機碼值組讀取設定資料：</span><span class="sxs-lookup"><span data-stu-id="8739b-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8739b-107">設定檔案，例如 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8739b-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8739b-108">環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-108">Environment variables</span></span>
* <span data-ttu-id="8739b-109">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="8739b-109">Azure Key Vault</span></span>
* <span data-ttu-id="8739b-110">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="8739b-110">Azure App Configuration</span></span>
* <span data-ttu-id="8739b-111">命令列引數</span><span class="sxs-lookup"><span data-stu-id="8739b-111">Command-line arguments</span></span>
* <span data-ttu-id="8739b-112">自訂提供者、已安裝或已建立</span><span class="sxs-lookup"><span data-stu-id="8739b-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="8739b-113">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-113">Directory files</span></span>
* <span data-ttu-id="8739b-114">記憶體內部 .NET 物件</span><span class="sxs-lookup"><span data-stu-id="8739b-114">In-memory .NET objects</span></span>

<span data-ttu-id="8739b-115">本主題提供 ASP.NET Core 中設定的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="8739b-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="8739b-116">如需在主控台應用程式中使用設定的詳細資訊，請參閱 [.net](/dotnet/core/extensions/configuration)設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="8739b-117">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="8739b-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8739b-118">預設組態</span><span class="sxs-lookup"><span data-stu-id="8739b-118">Default configuration</span></span>

<span data-ttu-id="8739b-119">ASP.NET Core 使用 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 建立的 web 應用程式會產生下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8739b-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 會以下列順序提供應用程式的預設組態：</span><span class="sxs-lookup"><span data-stu-id="8739b-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8739b-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) ：加入現有的 `IConfiguration` 作為來源。</span><span class="sxs-lookup"><span data-stu-id="8739b-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8739b-122">在預設設定案例中，新增 [主機](#hvac) 設定，並將其設定為 _應用程式_ 設定的第一個來源。</span><span class="sxs-lookup"><span data-stu-id="8739b-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8739b-123">[appsettings.json](#appsettingsjson) 使用 [JSON 設定提供者](#file-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="8739b-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8739b-124">*appsettings。* `Environment`使用 [json 設定提供者](#file-configuration-provider)的 *json。*</span><span class="sxs-lookup"><span data-stu-id="8739b-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8739b-125">例如， *appsettings*。***生產 \* \* _._json* 與 *appsettings*。 \* \* \* 開發** _._json \*。</span><span class="sxs-lookup"><span data-stu-id="8739b-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="8739b-126">應用程式在環境中執行時的[應用程式秘密](xref:security/app-secrets) `Development` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8739b-127">使用 [環境變數設定提供者](#evcp)的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8739b-128">使用 [命令列設定提供者](#command-line)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8739b-129">稍後新增的設定提供者會覆寫先前的金鑰設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8739b-130">例如，如果 `MyKey` 在和環境中設定 *appsettings.json* ，則會使用環境值。</span><span class="sxs-lookup"><span data-stu-id="8739b-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8739b-131">使用預設的設定提供者，  [命令列設定提供者](#clcp) 會覆寫所有其他提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="8739b-132">如需的詳細資訊 `CreateDefaultBuilder` ，請參閱 [預設 builder 設定](xref:fundamentals/host/generic-host#default-builder-settings)。</span><span class="sxs-lookup"><span data-stu-id="8739b-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8739b-133">下列程式碼會依新增的順序顯示已啟用的設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="8739b-134">請考慮下列檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8739b-135">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-136">預設會 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 以下列順序載入設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="8739b-137">*appsettings。* `Environment`*. json* ：例如 *appsettings*。\*\**生產 \* \* _._json* 和 \*appsettings\*\*\*\* \* \* 開發 _._json \* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="8739b-138">檔案的環境版本是根據 [IHostingEnvironment EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)載入。</span><span class="sxs-lookup"><span data-stu-id="8739b-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8739b-139">如需詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8739b-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8739b-140">*appsettings*... `Environment`*json* 值會覆寫中的索引鍵 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8739b-141">例如，根據預設：</span><span class="sxs-lookup"><span data-stu-id="8739b-141">For example, by default:</span></span>

* <span data-ttu-id="8739b-142">在開發期間， *appsettings*. \***開發** _._json \* 設定會覆寫在中找到的值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8739b-143">在生產環境中， *appsettings*. \***生產** _._json \* 設定會覆寫在中找到的值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8739b-144">例如，將應用程式部署至 Azure 時。</span><span class="sxs-lookup"><span data-stu-id="8739b-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8739b-145">使用選項模式系結階層式設定資料</span><span class="sxs-lookup"><span data-stu-id="8739b-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="8739b-146">使用 [預設](#default)設定， *appsettings.json* 以及 *appsettings。* `Environment`已啟用 [reloadOnChange： true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)的 *json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8739b-147">對和 appsettings 進行的變更 *appsettings.json* *。* `Environment`json 設定 [提供者](#jcp)會讀取應用程式啟動 ***後*** 的 *json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8739b-148">如需新增其他 JSON 設定檔的相關資訊，請參閱本檔中的 [json 設定提供者](#jcp) 。</span><span class="sxs-lookup"><span data-stu-id="8739b-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="8739b-149">合併服務集合</span><span class="sxs-lookup"><span data-stu-id="8739b-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="8739b-150">安全性與使用者秘密</span><span class="sxs-lookup"><span data-stu-id="8739b-150">Security and user secrets</span></span>

<span data-ttu-id="8739b-151">設定資料指導方針：</span><span class="sxs-lookup"><span data-stu-id="8739b-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="8739b-152">永遠不要將密碼或其他敏感性資料儲存在設定提供者程式碼或純文字設定檔中。</span><span class="sxs-lookup"><span data-stu-id="8739b-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8739b-153">[秘密管理員](xref:security/app-secrets)工具可以用來在開發中儲存秘密。</span><span class="sxs-lookup"><span data-stu-id="8739b-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="8739b-154">不要在開發或測試環境中使用生產環境祕密。</span><span class="sxs-lookup"><span data-stu-id="8739b-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8739b-155">請在專案外部指定祕密，以防止其意外認可至開放原始碼存放庫。</span><span class="sxs-lookup"><span data-stu-id="8739b-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8739b-156">根據 [預設](#default)，使用者秘密設定來源會在 JSON 設定來源之後註冊。</span><span class="sxs-lookup"><span data-stu-id="8739b-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="8739b-157">因此，使用者秘密金鑰優先于和 appsettings 中的金鑰 *appsettings.json* *。* `Environment`*. json*。</span><span class="sxs-lookup"><span data-stu-id="8739b-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8739b-158">如需有關儲存密碼或其他敏感性資料的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="8739b-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8739b-159"><xref:security/app-secrets>：包含使用環境變數來儲存敏感性資料的建議。</span><span class="sxs-lookup"><span data-stu-id="8739b-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8739b-160">秘密管理員工具會使用檔案設定 [提供者](#fcp) ，將使用者秘密儲存在本機系統上的 JSON 檔案中。</span><span class="sxs-lookup"><span data-stu-id="8739b-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8739b-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可安全地儲存 ASP.NET Core 應用程式的應用程式祕密。</span><span class="sxs-lookup"><span data-stu-id="8739b-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8739b-162">如需詳細資訊，請參閱<xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8739b-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8739b-163">環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-163">Environment variables</span></span>

<span data-ttu-id="8739b-164">使用 [預設](#default)設定時，會在 <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 讀取之後從環境變數機碼值組載入設定 *appsettings.json* （ *appsettings）。* `Environment`*. json* 和 [使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8739b-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="8739b-165">因此，從環境讀取的索引鍵值會覆寫 appsettings 中讀取的值 *appsettings.json* *。* `Environment`*. json* 和使用者秘密。</span><span class="sxs-lookup"><span data-stu-id="8739b-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8739b-166">下列 `set` 命令：</span><span class="sxs-lookup"><span data-stu-id="8739b-166">The following `set` commands:</span></span>

* <span data-ttu-id="8739b-167">在 Windows 上設定 [上述範例](#appsettingsjson) 的環境索引鍵和值。</span><span class="sxs-lookup"><span data-stu-id="8739b-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8739b-168">使用 [範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)時，測試設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8739b-169">`dotnet run`命令必須在專案目錄中執行。</span><span class="sxs-lookup"><span data-stu-id="8739b-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8739b-170">先前的環境設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-170">The preceding environment settings:</span></span>

* <span data-ttu-id="8739b-171">只會在從其設定的命令視窗啟動的進程中設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8739b-172">使用 Visual Studio 啟動的瀏覽器將無法讀取。</span><span class="sxs-lookup"><span data-stu-id="8739b-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8739b-173">您可以使用下列的 [setx](/windows-server/administration/windows-commands/setx) 命令，在 Windows 上設定環境機碼和值。</span><span class="sxs-lookup"><span data-stu-id="8739b-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8739b-174">與不同 `set` 的 `setx` 是，設定會持續保存。</span><span class="sxs-lookup"><span data-stu-id="8739b-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8739b-175">`/M` 設定系統內容中的變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8739b-176">如果 `/M` 未使用此參數，則會設定使用者環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8739b-177">若要測試上述命令是否覆寫 *appsettings.json* 並 *appsettings。* `Environment`*. json*：</span><span class="sxs-lookup"><span data-stu-id="8739b-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8739b-178">使用 Visual Studio： Exit 並重新啟動 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="8739b-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8739b-179">使用 CLI：啟動新的命令視窗，然後輸入 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8739b-180"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>使用字串呼叫以指定環境變數的前置詞：</span><span class="sxs-lookup"><span data-stu-id="8739b-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8739b-181">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="8739b-181">In the preceding code:</span></span>

* <span data-ttu-id="8739b-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 會在預設設定 [提供者](#default)之後加入。</span><span class="sxs-lookup"><span data-stu-id="8739b-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8739b-183">如需排序設定提供者的範例，請參閱 [JSON 設定提供者](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="8739b-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8739b-184">具有前置詞的環境變數會覆 `MyCustomPrefix_` 寫預設的設定 [提供者](#default)。</span><span class="sxs-lookup"><span data-stu-id="8739b-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8739b-185">這包括沒有前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8739b-186">讀取設定機碼值組時，會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="8739b-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8739b-187">下列命令會測試自訂前置詞：</span><span class="sxs-lookup"><span data-stu-id="8739b-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8739b-188">[預設](#default)設定會載入前面加上和的環境變數和命令列引數 `DOTNET_` `ASPNETCORE_` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8739b-189">和前置詞 `DOTNET_` `ASPNETCORE_` 是由 ASP.NET Core 用於 [主機和應用程式](xref:fundamentals/host/generic-host#host-configuration)設定，但不適用於使用者設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8739b-190">如需有關主機和應用程式設定的詳細資訊，請參閱 [.Net 泛型主機](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="8739b-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8739b-191">在 [Azure App Service](https://azure.microsoft.com/services/app-service/)上，選取 [**設定 > 設定**] 頁面上的 [**新增應用程式設定**]。</span><span class="sxs-lookup"><span data-stu-id="8739b-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8739b-192">Azure App Service 的應用程式設定如下：</span><span class="sxs-lookup"><span data-stu-id="8739b-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8739b-193">靜態加密，並透過加密通道傳輸。</span><span class="sxs-lookup"><span data-stu-id="8739b-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8739b-194">公開為環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-194">Exposed as environment variables.</span></span>

<span data-ttu-id="8739b-195">如需詳細資訊，請參閱 [Azure App：使用 Azure 入口網站覆寫應用程式設定](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="8739b-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8739b-196">如需 Azure 資料庫連接字串的相關資訊，請參閱 [連接字串](#constr) 前置詞。</span><span class="sxs-lookup"><span data-stu-id="8739b-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="8739b-197">環境變數的命名</span><span class="sxs-lookup"><span data-stu-id="8739b-197">Naming of environment variables</span></span>

<span data-ttu-id="8739b-198">環境變數名稱會反映檔案的結構 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="8739b-199">階層中的每個元素會以雙底線分隔 (偏好) 或冒號。</span><span class="sxs-lookup"><span data-stu-id="8739b-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="8739b-200">當元素結構包含陣列時，應該將陣列索引視為這個路徑中的其他元素名稱。</span><span class="sxs-lookup"><span data-stu-id="8739b-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="8739b-201">請考慮下列檔案 *appsettings.json* 及其對等的值（以環境變數表示）。</span><span class="sxs-lookup"><span data-stu-id="8739b-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="8739b-202">**環境變數**</span><span class="sxs-lookup"><span data-stu-id="8739b-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="8739b-203">在產生的 launchSettings.js中設定的環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-203">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="8739b-204">在 *launchSettings.js* 中設定的環境變數會覆寫系統內容中所設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="8739b-205">例如，ASP.NET Core web 範本會在將端點設定設定為的檔案 *上產生launchSettings.js* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-205">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="8739b-206">設定會 `applicationUrl` 設定 `ASPNETCORE_URLS` 環境變數，並覆寫環境中設定的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-206">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="8739b-207">在 Linux 上的 Escape 環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-207">Escape environment variables on Linux</span></span>

<span data-ttu-id="8739b-208">在 Linux 上，必須將 URL 環境變數的值換成 `systemd` 可加以剖析。</span><span class="sxs-lookup"><span data-stu-id="8739b-208">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="8739b-209">使用產生的 linux 工具 `systemd-escape``http:--localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="8739b-209">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="8739b-210">顯示環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-210">Display environment variables</span></span>

<span data-ttu-id="8739b-211">下列程式碼會顯示應用程式啟動時的環境變數和值，這在進行環境設定的偵錯工具時很有用：</span><span class="sxs-lookup"><span data-stu-id="8739b-211">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8739b-212">命令列</span><span class="sxs-lookup"><span data-stu-id="8739b-212">Command-line</span></span>

<span data-ttu-id="8739b-213">使用 [預設](#default) 設定時，會 <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 從命令列引數索引鍵/值組在下列設定來源之後載入設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-213">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8739b-214">*appsettings.json* 和 *appsettings*。 `Environment`*json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-214">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8739b-215">開發環境中的[應用程式秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8739b-215">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739b-216">環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-216">Environment variables.</span></span>

<span data-ttu-id="8739b-217">根據 [預設](#default)，在命令列覆寫設定值設定的設定值會與所有其他設定提供者一起設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-217">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8739b-218">命令列引數</span><span class="sxs-lookup"><span data-stu-id="8739b-218">Command-line arguments</span></span>

<span data-ttu-id="8739b-219">下列命令會使用下列命令來設定索引鍵和值 `=` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-219">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8739b-220">下列命令會使用下列命令來設定索引鍵和值 `/` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-220">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8739b-221">下列命令會使用下列命令來設定索引鍵和值 `--` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-221">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8739b-222">索引鍵值：</span><span class="sxs-lookup"><span data-stu-id="8739b-222">The key value:</span></span>

* <span data-ttu-id="8739b-223">必須遵循 `=` ，或者 `--` `/` 當值在空格後面時，索引鍵必須有或的前置詞。</span><span class="sxs-lookup"><span data-stu-id="8739b-223">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8739b-224">如果 `=` 使用，則不需要。</span><span class="sxs-lookup"><span data-stu-id="8739b-224">Isn't required if `=` is used.</span></span> <span data-ttu-id="8739b-225">例如： `MySetting=` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-225">For example, `MySetting=`.</span></span>

<span data-ttu-id="8739b-226">在相同的命令中，不要混用 `=` 與使用空格的索引鍵/值組搭配使用的命令列引數索引鍵/值配對。</span><span class="sxs-lookup"><span data-stu-id="8739b-226">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8739b-227">切換對應</span><span class="sxs-lookup"><span data-stu-id="8739b-227">Switch mappings</span></span>

<span data-ttu-id="8739b-228">交換器對應允許索引 **鍵** 名稱取代邏輯。</span><span class="sxs-lookup"><span data-stu-id="8739b-228">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8739b-229">為方法提供切換取代的字典 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 。</span><span class="sxs-lookup"><span data-stu-id="8739b-229">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8739b-230">使用切換對應字典時，會檢查字典中是否有任何索引鍵符合命令列引數所提供的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="8739b-230">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8739b-231">如果在字典中找到命令列索引鍵，則會傳回字典值以將索引鍵/值組設定為應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-231">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8739b-232">所有前面加上單虛線 (`-`) 的命令列索引鍵都需要切換對應。</span><span class="sxs-lookup"><span data-stu-id="8739b-232">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8739b-233">切換對應字典索引鍵規則：</span><span class="sxs-lookup"><span data-stu-id="8739b-233">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8739b-234">參數的開頭必須是 `-` 或 `--` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-234">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8739b-235">切換對應字典不能包含重複索引鍵。</span><span class="sxs-lookup"><span data-stu-id="8739b-235">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8739b-236">若要使用切換對應字典，請將它傳遞到的呼叫中 `AddCommandLine` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-236">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8739b-237">下列程式碼顯示已取代金鑰的索引鍵值：</span><span class="sxs-lookup"><span data-stu-id="8739b-237">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-238">下列命令適用于測試金鑰取代：</span><span class="sxs-lookup"><span data-stu-id="8739b-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8739b-239">針對使用切換對應的應用程式，呼叫 `CreateDefaultBuilder` 不應傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8739b-240">`CreateDefaultBuilder`方法的 `AddCommandLine` 呼叫不包含對應的參數，而且沒有任何方法可將切換對應字典傳遞給 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739b-241">解決方案不會將引數傳遞至， `CreateDefaultBuilder` 而是會允許 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法處理引數和切換對應字典。</span><span class="sxs-lookup"><span data-stu-id="8739b-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8739b-242">階層式設定資料</span><span class="sxs-lookup"><span data-stu-id="8739b-242">Hierarchical configuration data</span></span>

<span data-ttu-id="8739b-243">設定 API 會透過使用設定金鑰中的分隔符號來簡維階層式資料，以讀取階層式設定資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8739b-244">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8739b-245">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示數個設定設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-246">讀取階層式設定資料的慣用方式是使用選項模式。</span><span class="sxs-lookup"><span data-stu-id="8739b-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8739b-247">如需詳細資訊，請參閱本檔中的系結階層式設定 [資料](#optpat) 。</span><span class="sxs-lookup"><span data-stu-id="8739b-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8739b-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 與 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用來在設定資料中隔離區段與區段的子系。</span><span class="sxs-lookup"><span data-stu-id="8739b-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8739b-249">[GetSection,、 GetChildren 與 Exists](#getsection) 中說明這些方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8739b-250">設定機碼和值</span><span class="sxs-lookup"><span data-stu-id="8739b-250">Configuration keys and values</span></span>

<span data-ttu-id="8739b-251">設定金鑰：</span><span class="sxs-lookup"><span data-stu-id="8739b-251">Configuration keys:</span></span>

* <span data-ttu-id="8739b-252">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="8739b-252">Are case-insensitive.</span></span> <span data-ttu-id="8739b-253">例如，`ConnectionString` 與 `connectionstring` 會被視為相等的機碼。</span><span class="sxs-lookup"><span data-stu-id="8739b-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8739b-254">如果在多個設定提供者中設定索引鍵和值，則會使用最後新增的提供者所提供的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8739b-255">如需詳細資訊，請參閱 [預設](#default)設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8739b-256">階層式機碼</span><span class="sxs-lookup"><span data-stu-id="8739b-256">Hierarchical keys</span></span>
  * <span data-ttu-id="8739b-257">在設定 API 內，冒號分隔字元 (`:`) 可在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="8739b-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8739b-258">在環境變數中，冒號分隔字元可能無法在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="8739b-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8739b-259">所有平臺都支援雙底線、 `__` ，而且會自動轉換為冒號 `:` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8739b-260">在 Azure Key Vault 中，階層式索引鍵會使用 `--` 做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="8739b-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8739b-261">[](xref:security/key-vault-configuration) `--` `:` 當秘密載入至應用程式的設定時，Azure Key Vault 設定提供者會自動取代為。</span><span class="sxs-lookup"><span data-stu-id="8739b-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8739b-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支援在設定機碼中使用陣列索引將陣列繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="8739b-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739b-263">[將陣列繫結到類別](#boa)一節說明陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="8739b-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8739b-264">設定值：</span><span class="sxs-lookup"><span data-stu-id="8739b-264">Configuration values:</span></span>

* <span data-ttu-id="8739b-265">為字串。</span><span class="sxs-lookup"><span data-stu-id="8739b-265">Are strings.</span></span>
* <span data-ttu-id="8739b-266">Null 值無法存放在設定中或繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="8739b-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8739b-267">設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-267">Configuration providers</span></span>

<span data-ttu-id="8739b-268">下表顯示可供 ASP.NET Core 應用程式使用的設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8739b-269">提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-269">Provider</span></span> | <span data-ttu-id="8739b-270">從提供設定</span><span class="sxs-lookup"><span data-stu-id="8739b-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="8739b-271">Azure Key Vault 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="8739b-272">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="8739b-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="8739b-273">Azure App 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="8739b-274">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="8739b-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="8739b-275">命令列設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="8739b-276">命令列參數</span><span class="sxs-lookup"><span data-stu-id="8739b-276">Command-line parameters</span></span> |
| [<span data-ttu-id="8739b-277">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8739b-278">自訂來源</span><span class="sxs-lookup"><span data-stu-id="8739b-278">Custom source</span></span> |
| [<span data-ttu-id="8739b-279">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="8739b-280">環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-280">Environment variables</span></span> |
| [<span data-ttu-id="8739b-281">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8739b-282">INI、JSON 和 XML 檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="8739b-283">每個檔案的金鑰配置提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8739b-284">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-284">Directory files</span></span> |
| [<span data-ttu-id="8739b-285">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8739b-286">記憶體內集合</span><span class="sxs-lookup"><span data-stu-id="8739b-286">In-memory collections</span></span> |
| [<span data-ttu-id="8739b-287">使用者祕密</span><span class="sxs-lookup"><span data-stu-id="8739b-287">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="8739b-288">使用者設定檔目錄中的檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-288">File in the user profile directory</span></span> |

<span data-ttu-id="8739b-289">設定來源會依照其設定提供者的指定順序讀取。</span><span class="sxs-lookup"><span data-stu-id="8739b-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8739b-290">在程式碼中訂購設定提供者，以符合應用程式所需之基礎設定來源的優先順序。</span><span class="sxs-lookup"><span data-stu-id="8739b-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8739b-291">典型的設定提供者順序是：</span><span class="sxs-lookup"><span data-stu-id="8739b-291">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="8739b-292">*appsettings*... `Environment`*json*</span><span class="sxs-lookup"><span data-stu-id="8739b-292">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8739b-293">使用者祕密</span><span class="sxs-lookup"><span data-stu-id="8739b-293">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8739b-294">使用 [環境變數設定提供者](#evcp)的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-294">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8739b-295">使用 [命令列設定提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-295">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8739b-296">常見的作法是將命令列設定提供者新增至一系列的提供者，以允許命令列引數覆寫其他提供者所設定的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-296">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8739b-297">先前的提供者序列會用於 [預設](#default)設定中。</span><span class="sxs-lookup"><span data-stu-id="8739b-297">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8739b-298">連接字串前置詞</span><span class="sxs-lookup"><span data-stu-id="8739b-298">Connection string prefixes</span></span>

<span data-ttu-id="8739b-299">設定 API 有四個連接字串環境變數的特殊處理規則。</span><span class="sxs-lookup"><span data-stu-id="8739b-299">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8739b-300">這些連接字串涉及設定應用程式環境的 Azure 連接字串。</span><span class="sxs-lookup"><span data-stu-id="8739b-300">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8739b-301">具有資料表中所顯示前置詞的環境變數會以 [預設](#default) 設定載入至應用程式，或不提供任何前置詞 `AddEnvironmentVariables` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-301">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8739b-302">連接字串前置詞</span><span class="sxs-lookup"><span data-stu-id="8739b-302">Connection string prefix</span></span> | <span data-ttu-id="8739b-303">提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-303">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8739b-304">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-304">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8739b-305">MySQL</span><span class="sxs-lookup"><span data-stu-id="8739b-305">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8739b-306">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8739b-306">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8739b-307">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8739b-307">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8739b-308">當探索到具有下表所顯示之任何四個前置詞的環境變數並將其載入到設定中時：</span><span class="sxs-lookup"><span data-stu-id="8739b-308">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8739b-309">會透過移除環境變數前置詞並新增設定機碼區段 (`ConnectionStrings`) 來移除具有下表顯示之前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-309">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8739b-310">會建立新的設定機碼值組以代表資料庫連線提供者 (`CUSTOMCONNSTR_` 除外，這沒有所述提供者)。</span><span class="sxs-lookup"><span data-stu-id="8739b-310">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8739b-311">環境變數機碼</span><span class="sxs-lookup"><span data-stu-id="8739b-311">Environment variable key</span></span> | <span data-ttu-id="8739b-312">已轉換的設定機碼</span><span class="sxs-lookup"><span data-stu-id="8739b-312">Converted configuration key</span></span> | <span data-ttu-id="8739b-313">提供者設定項目</span><span class="sxs-lookup"><span data-stu-id="8739b-313">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-314">設定項目未建立。</span><span class="sxs-lookup"><span data-stu-id="8739b-314">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-315">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8739b-315">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739b-316">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739b-316">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-317">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8739b-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739b-318">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739b-318">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-319">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8739b-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739b-320">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739b-320">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8739b-321">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-321">File configuration provider</span></span>

<span data-ttu-id="8739b-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是用於從檔案系統載入設定的基底類別。</span><span class="sxs-lookup"><span data-stu-id="8739b-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8739b-323">以下是衍生自的設定提供者 `FileConfigurationProvider` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-323">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8739b-324">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-324">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8739b-325">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-325">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8739b-326">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-326">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8739b-327">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-327">INI configuration provider</span></span>

<span data-ttu-id="8739b-328"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 會在執行階段從 INI 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-328">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739b-329">下列程式碼會清除所有設定提供者，並新增數個設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-329">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8739b-330">在上述程式碼中， *MyIniConfig.ini* 和  *MyIniConfig* 中的設定 `Environment` 。中的設定會覆寫 *ini* 檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-330">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8739b-331">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-331">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8739b-332">[命令列設定提供者](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8739b-332">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739b-333">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列 *MyIniConfig.ini* 檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-333">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8739b-334">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-334">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8739b-335">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-335">JSON configuration provider</span></span>

<span data-ttu-id="8739b-336">會 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 從 JSON 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-336">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8739b-337">多載可以指定：</span><span class="sxs-lookup"><span data-stu-id="8739b-337">Overloads can specify:</span></span>

* <span data-ttu-id="8739b-338">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="8739b-338">Whether the file is optional.</span></span>
* <span data-ttu-id="8739b-339">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-339">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8739b-340">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-340">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8739b-341">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-341">The preceding code:</span></span>

* <span data-ttu-id="8739b-342">設定 JSON 設定提供者，以使用下列選項來載入檔案 *上的MyConfig.js* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-342">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8739b-343">`optional: true`：檔案是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="8739b-343">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8739b-344">`reloadOnChange: true` ：儲存變更時，會重載該檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-344">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8739b-345">在 *MyConfig.json* 檔案之前讀取 [預設設定提供者](#default)。</span><span class="sxs-lookup"><span data-stu-id="8739b-345">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8739b-346">預設設定提供者中的 [檔案覆寫] *MyConfig.js* 設定，包括 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8739b-346">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739b-347">您通常 ***不*** 希望自訂 JSON 檔案覆寫 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)中所設定的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-347">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739b-348">下列程式碼會清除所有設定提供者，並新增數個設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8739b-349">在上述程式碼中， *MyConfig.js* 中的設定和 *myconfig.xml*。 `Environment`*json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-349">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8739b-350">覆寫 *appsettings.json* 和 *appsettings* `Environment` 中的設定。*json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-350">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8739b-351">由 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)中的設定覆寫。</span><span class="sxs-lookup"><span data-stu-id="8739b-351">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739b-352">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含檔案上的下列 *MyConfig.js* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8739b-353">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8739b-354">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-354">XML configuration provider</span></span>

<span data-ttu-id="8739b-355"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 會在執行階段從 XML 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-355">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739b-356">下列程式碼會清除所有設定提供者，並新增數個設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-356">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8739b-357">在上述程式碼中， *MyXMLFile.xml* 和  *MyXMLFile* 中的設定 `Environment` 。中的設定會覆寫 *xml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-357">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8739b-358">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-358">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8739b-359">[命令列設定提供者](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8739b-359">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739b-360">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列 *MyXMLFile.xml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-360">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8739b-361">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-361">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-362">若 `name` 屬性是用來區別元素，則可以重複那些使用相同元素名稱的元素：</span><span class="sxs-lookup"><span data-stu-id="8739b-362">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8739b-363">下列程式碼會讀取先前的設定檔，並顯示金鑰和值：</span><span class="sxs-lookup"><span data-stu-id="8739b-363">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-364">屬性可用來提供值：</span><span class="sxs-lookup"><span data-stu-id="8739b-364">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8739b-365">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-365">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739b-366">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739b-366">key:attribute</span></span>
* <span data-ttu-id="8739b-367">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739b-367">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8739b-368">每個檔案的金鑰配置提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-368">Key-per-file configuration provider</span></span>

<span data-ttu-id="8739b-369"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目錄的檔案做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-369">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8739b-370">機碼是檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="8739b-370">The key is the file name.</span></span> <span data-ttu-id="8739b-371">值包含檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="8739b-371">The value contains the file's contents.</span></span> <span data-ttu-id="8739b-372">每個檔案的每個檔案設定提供者都是在 Docker 裝載案例中使用。</span><span class="sxs-lookup"><span data-stu-id="8739b-372">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8739b-373">若要啟用每個檔案機碼設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-373">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8739b-374">檔案的 `directoryPath` 必須是絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="8739b-374">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8739b-375">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="8739b-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739b-376">設定來源的 `Action<KeyPerFileConfigurationSource>` 委派。</span><span class="sxs-lookup"><span data-stu-id="8739b-376">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8739b-377">目錄是否為選擇性與目錄的路徑。</span><span class="sxs-lookup"><span data-stu-id="8739b-377">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8739b-378">雙底線 (`__`) 是做為檔案名稱中的設定金鑰分隔符號使用。</span><span class="sxs-lookup"><span data-stu-id="8739b-378">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8739b-379">例如，檔案名稱 `Logging__LogLevel__System` 會產生設定金鑰 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="8739b-379">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8739b-380">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-380">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8739b-381">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-381">Memory configuration provider</span></span>

<span data-ttu-id="8739b-382"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用記憶體內集合做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-382">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8739b-383">下列程式碼會將記憶體集合新增至設定系統：</span><span class="sxs-lookup"><span data-stu-id="8739b-383">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8739b-384">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述設定設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-384">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-385">在上述程式碼中， `config.AddInMemoryCollection(Dict)` 是在 [預設設定提供者](#default)之後加入。</span><span class="sxs-lookup"><span data-stu-id="8739b-385">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8739b-386">如需排序設定提供者的範例，請參閱 [JSON 設定提供者](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="8739b-386">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8739b-387">請參閱使用 [將陣列](#boa) 系結到另一個範例 `MemoryConfigurationProvider` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-387">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="8739b-388">Kestrel 端點組態</span><span class="sxs-lookup"><span data-stu-id="8739b-388">Kestrel endpoint configuration</span></span>

<span data-ttu-id="8739b-389">Kestrel 特定的端點設定會覆寫所有 [跨伺服器](xref:fundamentals/servers/index) 端點設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-389">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="8739b-390">跨伺服器端點設定包括：</span><span class="sxs-lookup"><span data-stu-id="8739b-390">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="8739b-391">UseUrls</span><span class="sxs-lookup"><span data-stu-id="8739b-391">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="8739b-392">`--urls`在[命令列](xref:fundamentals/configuration/index#command-line)上</span><span class="sxs-lookup"><span data-stu-id="8739b-392">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="8739b-393">[環境變數](xref:fundamentals/configuration/index#environment-variables)`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="8739b-393">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="8739b-394">請考慮 *appsettings.json* ASP.NET Core web 應用程式中使用的下列檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-394">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="8739b-395">當先前反白顯示的標記用於 ASP.NET Core web 應用程式 ***，並*** 在命令列上啟動應用程式時，會使用下列跨伺服器端點設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-395">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="8739b-396">Kestrel 會系結至) 中為檔案 (設定的端點 *appsettings.json* `https://localhost:9999` ，而不是 `https://localhost:7777` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-396">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="8739b-397">請考慮設定為環境變數的 Kestrel 特定端點：</span><span class="sxs-lookup"><span data-stu-id="8739b-397">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="8739b-398">在上述的環境變數中， `Https` 是 Kestrel 特定端點的名稱。</span><span class="sxs-lookup"><span data-stu-id="8739b-398">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="8739b-399">上述檔案 *appsettings.json* 也會定義名為的 Kestrel 特定端點 `Https` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-399">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="8739b-400">依 [預設](#default-configuration)，在 appsettings 之後，會讀取使用 [環境變數設定提供者](#evcp)的環境變數 *。* `Environment`因此 *，先前* 的環境變數會用於 `Https` 端點。</span><span class="sxs-lookup"><span data-stu-id="8739b-400">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="8739b-401">GetValue</span><span class="sxs-lookup"><span data-stu-id="8739b-401">GetValue</span></span>

<span data-ttu-id="8739b-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 從具有指定索引鍵的設定中解壓縮單一值，並將其轉換為指定的類型：</span><span class="sxs-lookup"><span data-stu-id="8739b-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-403">在上述程式碼中，如果在設定 `NumberKey` 中找不到， `99` 就會使用的預設值。</span><span class="sxs-lookup"><span data-stu-id="8739b-403">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8739b-404">GetSection、GetChildren 與 Exists</span><span class="sxs-lookup"><span data-stu-id="8739b-404">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8739b-405">針對接下來的範例，請考慮下列 *MySubsection.json* file：</span><span class="sxs-lookup"><span data-stu-id="8739b-405">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8739b-406">下列程式碼會將 *MySubsection.js* 新增至設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-406">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8739b-407">GetSection</span><span class="sxs-lookup"><span data-stu-id="8739b-407">GetSection</span></span>

<span data-ttu-id="8739b-408">[IConfiguration](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 會傳回具有指定之子區段索引鍵的設定子區段。</span><span class="sxs-lookup"><span data-stu-id="8739b-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8739b-409">下列程式碼會傳回的值 `section1` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-409">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-410">下列程式碼會傳回的值 `section2:subsection0` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-410">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-411">`GetSection` 絕不會傳回 `null`。</span><span class="sxs-lookup"><span data-stu-id="8739b-411">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8739b-412">若找不到相符的區段，會傳回空的 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="8739b-412">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8739b-413">當 `GetSection` 傳回相符區段時，未填入 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>。</span><span class="sxs-lookup"><span data-stu-id="8739b-413">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8739b-414">當區段存在時，會傳回 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 與 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>。</span><span class="sxs-lookup"><span data-stu-id="8739b-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8739b-415">GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="8739b-415">GetChildren and Exists</span></span>

<span data-ttu-id="8739b-416">下列程式碼會呼叫 [IConfiguration GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) ，並傳回 `section2:subsection0` 下列值：</span><span class="sxs-lookup"><span data-stu-id="8739b-416">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-417">上述程式碼會呼叫 [>microsoft.extensions.options.configurationextensions](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) ，以確認區段存在：</span><span class="sxs-lookup"><span data-stu-id="8739b-417">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8739b-418">系結陣列</span><span class="sxs-lookup"><span data-stu-id="8739b-418">Bind an array</span></span>

<span data-ttu-id="8739b-419">[ConfigurationBinder](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)支援使用設定索引鍵中的陣列索引，將陣列系結至物件。</span><span class="sxs-lookup"><span data-stu-id="8739b-419">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739b-420">任何公開數位索引鍵區段的陣列格式都能夠將陣列系結至 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 類別陣列。</span><span class="sxs-lookup"><span data-stu-id="8739b-420">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8739b-421">請考慮從 [範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中 *MyArray.js* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-421">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8739b-422">下列程式碼會將 *MyArray.js* 新增至設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-422">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8739b-423">下列程式碼會讀取設定，並顯示這些值：</span><span class="sxs-lookup"><span data-stu-id="8739b-423">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-424">上述程式碼會傳回下列輸出：</span><span class="sxs-lookup"><span data-stu-id="8739b-424">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8739b-425">在上述輸出中，索引3具有值 `value40` ，對應于 `"4": "value40",` *MyArray.js* 中的。</span><span class="sxs-lookup"><span data-stu-id="8739b-425">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8739b-426">系結陣列索引是連續的，不會系結至設定索引鍵索引。</span><span class="sxs-lookup"><span data-stu-id="8739b-426">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8739b-427">設定系結器無法系結 null 值，或在系結物件中建立 null 專案</span><span class="sxs-lookup"><span data-stu-id="8739b-427">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8739b-428">下列程式碼會 `array:entries` 使用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 擴充方法載入設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-428">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8739b-429">下列程式碼會讀取中的設定 `arrayDict` `Dictionary` ，並顯示這些值：</span><span class="sxs-lookup"><span data-stu-id="8739b-429">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-430">上述程式碼會傳回下列輸出：</span><span class="sxs-lookup"><span data-stu-id="8739b-430">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8739b-431">繫結物件中的索引 &num;3 存放 `array:4` 設定機碼與其 `value4` 的設定資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-431">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8739b-432">系結包含陣列的設定資料時，在建立物件時，會使用設定機碼中的陣列索引來反覆運算設定資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-432">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8739b-433">設定資料中不能保留 Null 值，當設定機碼中的陣列略過一或多個索引時，不會在繫結物件中建立 Null 值項目。</span><span class="sxs-lookup"><span data-stu-id="8739b-433">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8739b-434">在系 &num; 結至實例之前，您可以藉 `ArrayExample` 由讀取索引 &num; 3 索引鍵/值組的任何設定提供者，在系結至實例之前，提供索引3的遺漏設定專案。</span><span class="sxs-lookup"><span data-stu-id="8739b-434">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8739b-435">請考慮下列來自範例下載的檔案 *Value3.js* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-435">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8739b-436">下列程式碼包含與的 *Value3.js* 設定 `arrayDict` `Dictionary` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-436">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8739b-437">下列程式碼會讀取先前的設定，並顯示這些值：</span><span class="sxs-lookup"><span data-stu-id="8739b-437">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-438">上述程式碼會傳回下列輸出：</span><span class="sxs-lookup"><span data-stu-id="8739b-438">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8739b-439">自訂設定提供者不需要實作陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="8739b-439">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8739b-440">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-440">Custom configuration provider</span></span>

<span data-ttu-id="8739b-441">範例應用程式示範如何建立會使用 [Entity Framework (EF)](/ef/core/) 從資料庫讀取設定機碼值組的基本設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-441">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8739b-442">提供者具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="8739b-442">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8739b-443">EF 記憶體內資料庫會用於示範用途。</span><span class="sxs-lookup"><span data-stu-id="8739b-443">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8739b-444">若要使用需要連接字串的資料庫，請實作第二個 `ConfigurationBuilder` 以從另一個設定提供者提供連接字串。</span><span class="sxs-lookup"><span data-stu-id="8739b-444">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8739b-445">啟動時，該提供者會將資料庫資料表讀入到設定中。</span><span class="sxs-lookup"><span data-stu-id="8739b-445">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8739b-446">該提供者不會以個別機碼為基礎查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="8739b-446">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8739b-447">未實作變更時重新載入，因此在應用程式啟動後更新資料庫對應用程式設定沒有影響。</span><span class="sxs-lookup"><span data-stu-id="8739b-447">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8739b-448">定義 `EFConfigurationValue` 實體來在資料庫中存放設定值。</span><span class="sxs-lookup"><span data-stu-id="8739b-448">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8739b-449">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-449">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8739b-450">新增 `EFConfigurationContext` 以存放及存取已設定的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-450">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8739b-451">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8739b-452">建立會實作 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的類別。</span><span class="sxs-lookup"><span data-stu-id="8739b-452">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8739b-453">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8739b-454">透過繼承自 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 來建立自訂設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-454">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8739b-455">若資料庫是空的，設定提供者會初始化資料庫。</span><span class="sxs-lookup"><span data-stu-id="8739b-455">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8739b-456">由於設定索引 [鍵不區分大小寫](#keys)，因此用來初始化資料庫的字典會以不區分大小寫的比較子來建立 ([stringcomparison. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 。</span><span class="sxs-lookup"><span data-stu-id="8739b-456">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8739b-457">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8739b-458">`AddEFConfiguration` 擴充方法允許新增設定來源到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8739b-458">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8739b-459">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739b-459">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8739b-460">下列程式碼顯示如何在 *Program.cs* 中使用自訂 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="8739b-460">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8739b-461">在啟動時存取設定</span><span class="sxs-lookup"><span data-stu-id="8739b-461">Access configuration in Startup</span></span>

<span data-ttu-id="8739b-462">下列程式碼會在方法中顯示設定資料 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-462">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8739b-463">如需使用啟動方便方法來存取設定的範例，請參閱[應用程式啟動：方便方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="8739b-463">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="8739b-464">存取頁面中的設定 Razor</span><span class="sxs-lookup"><span data-stu-id="8739b-464">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8739b-465">下列程式碼會在頁面中顯示設定資料 Razor ：</span><span class="sxs-lookup"><span data-stu-id="8739b-465">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="8739b-466">在下列程式碼中， `MyOptions` 會新增至服務容器， <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 並系結至設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-466">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="8739b-467">下列標記會使用指示詞 [`@inject`](xref:mvc/views/razor#inject) Razor 來解析和顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="8739b-467">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8739b-468">MVC view 檔案中的存取設定</span><span class="sxs-lookup"><span data-stu-id="8739b-468">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8739b-469">下列程式碼會顯示 MVC 視圖中的設定資料：</span><span class="sxs-lookup"><span data-stu-id="8739b-469">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="8739b-470">使用委派設定選項</span><span class="sxs-lookup"><span data-stu-id="8739b-470">Configure options with a delegate</span></span>

<span data-ttu-id="8739b-471">在委派中設定的選項會覆寫設定提供者中所設定的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-471">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="8739b-472">使用委派來設定選項，會示範為範例應用程式中的範例2。</span><span class="sxs-lookup"><span data-stu-id="8739b-472">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="8739b-473">在下列程式碼中， <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務會新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="8739b-473">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8739b-474">它會使用委派來設定下列值 `MyOptions` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-474">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="8739b-475">下列程式碼會顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="8739b-475">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8739b-476">在上述範例中，和的值 `Option1` `Option2` 是在中指定， *appsettings.json* 然後由設定的委派覆寫。</span><span class="sxs-lookup"><span data-stu-id="8739b-476">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8739b-477">主機與應用程式組態的比較</span><span class="sxs-lookup"><span data-stu-id="8739b-477">Host versus app configuration</span></span>

<span data-ttu-id="8739b-478">設定及啟動應用程式之前，會先設定及啟動「主機」。</span><span class="sxs-lookup"><span data-stu-id="8739b-478">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8739b-479">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="8739b-479">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8739b-480">應用程式與主機都是使用此主題中所述的設定提供者來設定的。</span><span class="sxs-lookup"><span data-stu-id="8739b-480">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8739b-481">主機組態機碼/值組也會包含在應用程式的組態中。</span><span class="sxs-lookup"><span data-stu-id="8739b-481">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8739b-482">如需有關當建置主機時如何使用設定提供者的詳細資訊，以及設定來源如何影響主機設定的詳細資訊，請參閱 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="8739b-482">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8739b-483">預設主機設定</span><span class="sxs-lookup"><span data-stu-id="8739b-483">Default host configuration</span></span>

<span data-ttu-id="8739b-484">如需使用 [Web 主機](xref:fundamentals/host/web-host)時預設組態的詳細資料，請參閱[本主題的 ASP.NET Core 2.2 版本](?view=aspnetcore-2.2&preserve-view=true)。</span><span class="sxs-lookup"><span data-stu-id="8739b-484">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="8739b-485">主機組態的提供來源：</span><span class="sxs-lookup"><span data-stu-id="8739b-485">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8739b-486">前面加 `DOTNET_` 上 (的環境變數，例如， `DOTNET_ENVIRONMENT` 使用 [環境變數設定提供者](#environment-variables)) 。</span><span class="sxs-lookup"><span data-stu-id="8739b-486">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="8739b-487">載入設定機碼值組時，會移除前置詞 (`DOTNET_`)。</span><span class="sxs-lookup"><span data-stu-id="8739b-487">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8739b-488">使用 [命令列設定提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-488">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8739b-489">已建立 Web 主機預設組態 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="8739b-489">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8739b-490">Kestrel 會用作為網頁伺服器，並使用應用程式的組態提供者來設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-490">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8739b-491">新增主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="8739b-491">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8739b-492">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境變數設定為 `true`，則會新增轉接的標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="8739b-492">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8739b-493">啟用 IIS 整合。</span><span class="sxs-lookup"><span data-stu-id="8739b-493">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8739b-494">其他設定</span><span class="sxs-lookup"><span data-stu-id="8739b-494">Other configuration</span></span>

<span data-ttu-id="8739b-495">本主題僅適用于 *應用程式* 設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-495">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8739b-496">執行和主控 ASP.NET Core 應用程式的其他層面，是使用本主題未涵蓋的設定檔來設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-496">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8739b-497">*launch.js開啟* /*launchSettings.js* 為開發環境的工具設定檔，如下所述：</span><span class="sxs-lookup"><span data-stu-id="8739b-497">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8739b-498">在中 <xref:fundamentals/environments#development> 。</span><span class="sxs-lookup"><span data-stu-id="8739b-498">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8739b-499">在檔集中，用來設定開發案例 ASP.NET Core 應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-499">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8739b-500">*web.config* 是伺服器設定檔，如下列主題所述：</span><span class="sxs-lookup"><span data-stu-id="8739b-500">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8739b-501">在 *launchSettings.js* 中設定的環境變數會覆寫系統內容中所設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-501">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="8739b-502">如需從舊版 ASP.NET 遷移應用程式設定的詳細資訊，請參閱 <xref:migration/proper-to-2x/index#store-configurations> 。</span><span class="sxs-lookup"><span data-stu-id="8739b-502">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8739b-503">從外部組件新增設定</span><span class="sxs-lookup"><span data-stu-id="8739b-503">Add configuration from an external assembly</span></span>

<span data-ttu-id="8739b-504"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 實作允許在啟動時從應用程式 `Startup` 類別外部的外部組件，針對應用程式新增增強功能。</span><span class="sxs-lookup"><span data-stu-id="8739b-504">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8739b-505">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8739b-505">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8739b-506">其他資源</span><span class="sxs-lookup"><span data-stu-id="8739b-506">Additional resources</span></span>

* [<span data-ttu-id="8739b-507">設定來源程式碼</span><span class="sxs-lookup"><span data-stu-id="8739b-507">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8739b-508">ASP.NET Core 中的應用程式設定是以由 *設定提供者* 所建立的機碼值組為基礎。</span><span class="sxs-lookup"><span data-stu-id="8739b-508">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8739b-509">設定提供者會從各種設定來源將設定資料讀取到機碼值組中：</span><span class="sxs-lookup"><span data-stu-id="8739b-509">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8739b-510">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="8739b-510">Azure Key Vault</span></span>
* <span data-ttu-id="8739b-511">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="8739b-511">Azure App Configuration</span></span>
* <span data-ttu-id="8739b-512">命令列引數</span><span class="sxs-lookup"><span data-stu-id="8739b-512">Command-line arguments</span></span>
* <span data-ttu-id="8739b-513">自訂提供者 (已安裝或已建立)</span><span class="sxs-lookup"><span data-stu-id="8739b-513">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8739b-514">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-514">Directory files</span></span>
* <span data-ttu-id="8739b-515">環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-515">Environment variables</span></span>
* <span data-ttu-id="8739b-516">記憶體內部 .NET 物件</span><span class="sxs-lookup"><span data-stu-id="8739b-516">In-memory .NET objects</span></span>
* <span data-ttu-id="8739b-517">設定檔</span><span class="sxs-lookup"><span data-stu-id="8739b-517">Settings files</span></span>

<span data-ttu-id="8739b-518">一般組態提供者案例 ([microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) 的組態套件包含在 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="8739b-518">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8739b-519">下列程式碼範例與範例應用程式中的程式碼範例使用 <xref:Microsoft.Extensions.Configuration> 命名空間：</span><span class="sxs-lookup"><span data-stu-id="8739b-519">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8739b-520">*選項模式* 是此主題中所述之設定概念的延伸。</span><span class="sxs-lookup"><span data-stu-id="8739b-520">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8739b-521">選項使用類別來代表一組相關的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-521">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8739b-522">如需詳細資訊，請參閱<xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="8739b-522">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8739b-523">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="8739b-523">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8739b-524">主機與應用程式組態的比較</span><span class="sxs-lookup"><span data-stu-id="8739b-524">Host versus app configuration</span></span>

<span data-ttu-id="8739b-525">設定及啟動應用程式之前，會先設定及啟動「主機」。</span><span class="sxs-lookup"><span data-stu-id="8739b-525">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8739b-526">主機負責應用程式啟動和存留期管理。</span><span class="sxs-lookup"><span data-stu-id="8739b-526">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8739b-527">應用程式與主機都是使用此主題中所述的設定提供者來設定的。</span><span class="sxs-lookup"><span data-stu-id="8739b-527">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8739b-528">主機組態機碼/值組也會包含在應用程式的組態中。</span><span class="sxs-lookup"><span data-stu-id="8739b-528">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8739b-529">如需有關當建置主機時如何使用設定提供者的詳細資訊，以及設定來源如何影響主機設定的詳細資訊，請參閱 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="8739b-529">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8739b-530">其他設定</span><span class="sxs-lookup"><span data-stu-id="8739b-530">Other configuration</span></span>

<span data-ttu-id="8739b-531">本主題僅適用于 *應用程式* 設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-531">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8739b-532">執行和主控 ASP.NET Core 應用程式的其他層面，是使用本主題未涵蓋的設定檔來設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-532">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8739b-533">*launch.js開啟* /*launchSettings.js* 為開發環境的工具設定檔，如下所述：</span><span class="sxs-lookup"><span data-stu-id="8739b-533">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8739b-534">在中 <xref:fundamentals/environments#development> 。</span><span class="sxs-lookup"><span data-stu-id="8739b-534">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8739b-535">在檔集中，用來設定開發案例 ASP.NET Core 應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-535">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8739b-536">*web.config* 是伺服器設定檔，如下列主題所述：</span><span class="sxs-lookup"><span data-stu-id="8739b-536">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8739b-537">如需從舊版 ASP.NET 遷移應用程式設定的詳細資訊，請參閱 <xref:migration/proper-to-2x/index#store-configurations> 。</span><span class="sxs-lookup"><span data-stu-id="8739b-537">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8739b-538">預設組態</span><span class="sxs-lookup"><span data-stu-id="8739b-538">Default configuration</span></span>

<span data-ttu-id="8739b-539">以 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 範本為基礎的 Web 應用程式，會在建置主機時呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="8739b-539">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8739b-540">`CreateDefaultBuilder` 會以下列順序提供應用程式的預設組態：</span><span class="sxs-lookup"><span data-stu-id="8739b-540">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8739b-541">下列項目適用於使用 [Web 主機](xref:fundamentals/host/web-host)的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-541">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8739b-542">如需使用[一般主機](xref:fundamentals/host/generic-host)時預設組態的詳細資料，請參閱[本主題的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="8739b-542">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8739b-543">主機組態的提供來源：</span><span class="sxs-lookup"><span data-stu-id="8739b-543">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8739b-544">使用[環境變數組態提供者](#environment-variables-configuration-provider)且以 `ASPNETCORE_` 為前置詞 (例如 `ASPNETCORE_ENVIRONMENT`) 的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-544">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8739b-545">載入設定機碼值組時，會移除前置詞 (`ASPNETCORE_`)。</span><span class="sxs-lookup"><span data-stu-id="8739b-545">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8739b-546">使用[命令列組態提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-546">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8739b-547">應用程式設定的提供來源：</span><span class="sxs-lookup"><span data-stu-id="8739b-547">App configuration is provided from:</span></span>
  * <span data-ttu-id="8739b-548">*appsettings.json* 使用檔案設定 [提供者](#file-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="8739b-548">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8739b-549">使用 [檔案組態提供者](#file-configuration-provider)的 *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="8739b-549">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8739b-550">應用程式在使用輸入組件的 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8739b-550">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8739b-551">使用[環境變數組態提供者](#environment-variables-configuration-provider)的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-551">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8739b-552">使用[命令列組態提供者](#command-line-configuration-provider)的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-552">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8739b-553">安全性</span><span class="sxs-lookup"><span data-stu-id="8739b-553">Security</span></span>

<span data-ttu-id="8739b-554">採用下列做法來保護敏感性組態資料：</span><span class="sxs-lookup"><span data-stu-id="8739b-554">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8739b-555">永遠不要將密碼或其他敏感性資料儲存在設定提供者程式碼或純文字設定檔中。</span><span class="sxs-lookup"><span data-stu-id="8739b-555">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8739b-556">不要在開發或測試環境中使用生產環境祕密。</span><span class="sxs-lookup"><span data-stu-id="8739b-556">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8739b-557">請在專案外部指定祕密，以防止其意外認可至開放原始碼存放庫。</span><span class="sxs-lookup"><span data-stu-id="8739b-557">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8739b-558">如需詳細資訊，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="8739b-558">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8739b-559"><xref:security/app-secrets>：包含使用環境變數來儲存敏感性資料的建議。</span><span class="sxs-lookup"><span data-stu-id="8739b-559"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8739b-560">秘密管理員工具會使用檔案設定提供者，將使用者秘密儲存在本機系統上的 JSON 檔案中。</span><span class="sxs-lookup"><span data-stu-id="8739b-560">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8739b-561">此主題稍後將說明「檔案設定提供者」。</span><span class="sxs-lookup"><span data-stu-id="8739b-561">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8739b-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可安全地儲存 ASP.NET Core 應用程式的應用程式祕密。</span><span class="sxs-lookup"><span data-stu-id="8739b-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8739b-563">如需詳細資訊，請參閱<xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8739b-563">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8739b-564">階層式設定資料</span><span class="sxs-lookup"><span data-stu-id="8739b-564">Hierarchical configuration data</span></span>

<span data-ttu-id="8739b-565">設定 API 可在設定機碼中使用分隔符號來壓平合併階層式資料，以管理階層式設定資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-565">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8739b-566">在下列 JSON 檔案中，兩個區段的結構式階層中有四個機碼存在：</span><span class="sxs-lookup"><span data-stu-id="8739b-566">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8739b-567">當該檔案被讀入設定之後，會建立唯一機碼來維護設定來源的原始階層式資料結構。</span><span class="sxs-lookup"><span data-stu-id="8739b-567">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8739b-568">系統會使用冒號 (`:`) 將區段與機碼壓平合併以維護原始結構：</span><span class="sxs-lookup"><span data-stu-id="8739b-568">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8739b-569">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-569">section0:key0</span></span>
* <span data-ttu-id="8739b-570">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-570">section0:key1</span></span>
* <span data-ttu-id="8739b-571">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-571">section1:key0</span></span>
* <span data-ttu-id="8739b-572">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-572">section1:key1</span></span>

<span data-ttu-id="8739b-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 與 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用來在設定資料中隔離區段與區段的子系。</span><span class="sxs-lookup"><span data-stu-id="8739b-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8739b-574">[GetSection,、 GetChildren 與 Exists](#getsection-getchildren-and-exists) 中說明這些方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-574">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8739b-575">慣例</span><span class="sxs-lookup"><span data-stu-id="8739b-575">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8739b-576">來源和提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-576">Sources and providers</span></span>

<span data-ttu-id="8739b-577">在應用程式啟動時，會依照設定來源的設定提供者的指定順序讀入設定來源。</span><span class="sxs-lookup"><span data-stu-id="8739b-577">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8739b-578">實作變更偵測的組態提供者能夠在基礎設定變更時重新載入組態。</span><span class="sxs-lookup"><span data-stu-id="8739b-578">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8739b-579">例如，檔案組態提供者 (將於本主題稍後討論) 和 [Azure Key Vault 組態提供者](xref:security/key-vault-configuration)均會實作變更偵測。</span><span class="sxs-lookup"><span data-stu-id="8739b-579">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8739b-580">您可以在應用程式的[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中找到 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="8739b-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8739b-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可以插入至 Razor 頁面 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> ，以取得類別的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8739b-582">在下列範例中， `_config` 會使用欄位來存取設定值：</span><span class="sxs-lookup"><span data-stu-id="8739b-582">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8739b-583">設定提供者無法使用 DI，因為當它們由主機設定時，它無法使用。</span><span class="sxs-lookup"><span data-stu-id="8739b-583">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8739b-584">索引鍵</span><span class="sxs-lookup"><span data-stu-id="8739b-584">Keys</span></span>

<span data-ttu-id="8739b-585">設定機碼會採用下列慣例：</span><span class="sxs-lookup"><span data-stu-id="8739b-585">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8739b-586">機碼區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="8739b-586">Keys are case-insensitive.</span></span> <span data-ttu-id="8739b-587">例如，`ConnectionString` 與 `connectionstring` 會被視為相等的機碼。</span><span class="sxs-lookup"><span data-stu-id="8739b-587">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8739b-588">若相同機碼的值是由相同或不同設定提供者設定，在機碼上設定的最後一個值是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-588">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="8739b-589">如需重複的 JSON 金鑰的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/extensions/issues/2381)。</span><span class="sxs-lookup"><span data-stu-id="8739b-589">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="8739b-590">階層式機碼</span><span class="sxs-lookup"><span data-stu-id="8739b-590">Hierarchical keys</span></span>
  * <span data-ttu-id="8739b-591">在設定 API 內，冒號分隔字元 (`:`) 可在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="8739b-591">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8739b-592">在環境變數中，冒號分隔字元可能無法在所有平台上運作。</span><span class="sxs-lookup"><span data-stu-id="8739b-592">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8739b-593">所有平台都支援雙底線 (`__`)，且會自動轉換為冒號。</span><span class="sxs-lookup"><span data-stu-id="8739b-593">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8739b-594">在 Azure Key Vault 中，階層式機碼使用 `--` (兩個破折號) 來做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="8739b-594">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8739b-595">撰寫程式碼，以在將秘密載入至應用程式的設定時，以冒號取代虛線。</span><span class="sxs-lookup"><span data-stu-id="8739b-595">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8739b-596"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支援在設定機碼中使用陣列索引將陣列繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="8739b-596">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739b-597">[將陣列繫結到類別](#bind-an-array-to-a-class)一節說明陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="8739b-597">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8739b-598">值</span><span class="sxs-lookup"><span data-stu-id="8739b-598">Values</span></span>

<span data-ttu-id="8739b-599">設定值會採用下列慣例：</span><span class="sxs-lookup"><span data-stu-id="8739b-599">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8739b-600">值是字串。</span><span class="sxs-lookup"><span data-stu-id="8739b-600">Values are strings.</span></span>
* <span data-ttu-id="8739b-601">Null 值無法存放在設定中或繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="8739b-601">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8739b-602">提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-602">Providers</span></span>

<span data-ttu-id="8739b-603">下表顯示可供 ASP.NET Core 應用程式使用的設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-603">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8739b-604">提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-604">Provider</span></span> | <span data-ttu-id="8739b-605">從&hellip;提供設定</span><span class="sxs-lookup"><span data-stu-id="8739b-605">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8739b-606">[Azure Key Vault 設定提供者](xref:security/key-vault-configuration) (*安全性* 主題)</span><span class="sxs-lookup"><span data-stu-id="8739b-606">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8739b-607">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="8739b-607">Azure Key Vault</span></span> |
| <span data-ttu-id="8739b-608">[Azure 應用程式組態提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure 文件)</span><span class="sxs-lookup"><span data-stu-id="8739b-608">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8739b-609">Azure 應用程式組態</span><span class="sxs-lookup"><span data-stu-id="8739b-609">Azure App Configuration</span></span> |
| [<span data-ttu-id="8739b-610">命令列設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-610">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8739b-611">命令列參數</span><span class="sxs-lookup"><span data-stu-id="8739b-611">Command-line parameters</span></span> |
| [<span data-ttu-id="8739b-612">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-612">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8739b-613">自訂來源</span><span class="sxs-lookup"><span data-stu-id="8739b-613">Custom source</span></span> |
| [<span data-ttu-id="8739b-614">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-614">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8739b-615">環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-615">Environment variables</span></span> |
| [<span data-ttu-id="8739b-616">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-616">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8739b-617">檔案 (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="8739b-617">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8739b-618">每個檔案機碼的設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-618">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8739b-619">目錄檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-619">Directory files</span></span> |
| [<span data-ttu-id="8739b-620">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-620">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8739b-621">記憶體內集合</span><span class="sxs-lookup"><span data-stu-id="8739b-621">In-memory collections</span></span> |
| <span data-ttu-id="8739b-622"> (*安全性* 主題的 [使用者密碼](xref:security/app-secrets)) </span><span class="sxs-lookup"><span data-stu-id="8739b-622">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8739b-623">使用者設定檔目錄中的檔案</span><span class="sxs-lookup"><span data-stu-id="8739b-623">File in the user profile directory</span></span> |

<span data-ttu-id="8739b-624">在啟動時，會依照設定來源的設定提供者的指定順序讀入設定來源。</span><span class="sxs-lookup"><span data-stu-id="8739b-624">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8739b-625">本主題所描述的設定提供者會依字母順序描述，而不是依照程式碼排列順序。</span><span class="sxs-lookup"><span data-stu-id="8739b-625">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8739b-626">在程式碼中訂購設定提供者，以符合應用程式所需之基礎設定來源的優先順序。</span><span class="sxs-lookup"><span data-stu-id="8739b-626">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8739b-627">典型的設定提供者順序是：</span><span class="sxs-lookup"><span data-stu-id="8739b-627">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8739b-628">檔案 (*appsettings.json* ， *appsettings. {環境} json*，其中 `{Environment}` 是應用程式目前的裝載環境) </span><span class="sxs-lookup"><span data-stu-id="8739b-628">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8739b-629">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8739b-629">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8739b-630">僅)  (開發環境的[使用者秘密](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="8739b-630">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8739b-631">環境變數</span><span class="sxs-lookup"><span data-stu-id="8739b-631">Environment variables</span></span>
1. <span data-ttu-id="8739b-632">命令列引數</span><span class="sxs-lookup"><span data-stu-id="8739b-632">Command-line arguments</span></span>

<span data-ttu-id="8739b-633">將命令列組態提供者放在提供者序列結尾是常見做法，因為這樣可以讓命令列引數覆寫由其他提供者所設定的組態。</span><span class="sxs-lookup"><span data-stu-id="8739b-633">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8739b-634">當使用初始化新的主機建立器時，會使用上述的提供者序列 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-634">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739b-635">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="8739b-635">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8739b-636">使用 UseConfiguration 設定主機建立器</span><span class="sxs-lookup"><span data-stu-id="8739b-636">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8739b-637">若要設定主機建立器，請使用該組態在主機建立器上呼叫 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="8739b-637">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8739b-638">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8739b-638">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8739b-639">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定提供者，以及由 `CreateDefaultBuilder` 自動新增的設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-639">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8739b-640">使用命令列引數覆寫先前的組態</span><span class="sxs-lookup"><span data-stu-id="8739b-640">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8739b-641">若要提供可使用命令列引數覆寫的應用程式組態，請最後呼叫 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="8739b-641">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8739b-642">移除 >createdefaultbuilder 新增的提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-642">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8739b-643">若要移除新增的提供者 `CreateDefaultBuilder` ，請在[IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)上呼叫[Clear](/dotnet/api/system.collections.generic.icollection-1.clear) first：</span><span class="sxs-lookup"><span data-stu-id="8739b-643">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8739b-644">在應用程式啟動期間使用組態</span><span class="sxs-lookup"><span data-stu-id="8739b-644">Consume configuration during app startup</span></span>

<span data-ttu-id="8739b-645">應用程式啟動期間，可以使用 `ConfigureAppConfiguration` 中為應用程式提供的組態，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="8739b-645">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8739b-646">如需詳細資訊，請參閱[在啟動期間存取組態](#access-configuration-during-startup)一節。</span><span class="sxs-lookup"><span data-stu-id="8739b-646">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8739b-647">命令列設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-647">Command-line Configuration Provider</span></span>

<span data-ttu-id="8739b-648"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 會在執行階段從命令列引數機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-648">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8739b-649">為了啟用命令列設定，<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 延伸模組方法會在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫。</span><span class="sxs-lookup"><span data-stu-id="8739b-649">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739b-650">呼叫 `CreateDefaultBuilder(string [])` 時，會自動呼叫 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="8739b-650">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8739b-651">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="8739b-651">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739b-652">`CreateDefaultBuilder` 也會載入：</span><span class="sxs-lookup"><span data-stu-id="8739b-652">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8739b-653">自和 appsettings 的選擇性設定 *appsettings.json* *。環境}. json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-653">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8739b-654">開發環境中的[使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8739b-654">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739b-655">環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-655">Environment variables.</span></span>

<span data-ttu-id="8739b-656">`CreateDefaultBuilder` 會最後才新增命令列設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-656">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8739b-657">在執行階段傳遞的命令列引數會覆寫由其它提供者所設定的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-657">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8739b-658">`CreateDefaultBuilder` 會在建構主機時執行作業。</span><span class="sxs-lookup"><span data-stu-id="8739b-658">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8739b-659">因此，由`CreateDefaultBuilder` 啟用的命令列設定可以影響主機的設定方式。</span><span class="sxs-lookup"><span data-stu-id="8739b-659">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8739b-660">針對以 ASP.NET Core 範本為基礎的應用程式，`AddCommandLine` 已由 `CreateDefaultBuilder` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="8739b-660">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739b-661">若要新增其他組態提供者並維持能夠使用命令列引數覆寫這些提供者組態的能力，請在 `ConfigureAppConfiguration` 中呼叫應用程式的其他提供者，且最後呼叫 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="8739b-661">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8739b-662">**範例**</span><span class="sxs-lookup"><span data-stu-id="8739b-662">**Example**</span></span>

<span data-ttu-id="8739b-663">範例應用程式利用靜態方便方法 `CreateDefaultBuilder` 的優勢來建置主機，這包括對 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="8739b-663">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8739b-664">從專案目錄中開啟命令提示字元。</span><span class="sxs-lookup"><span data-stu-id="8739b-664">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8739b-665">提供命令列引數給 `dotnet run` 命令 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="8739b-665">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8739b-666">在應用程式執行之後，開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-666">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8739b-667">觀察輸出是否包含提供給 `dotnet run` 之設定命令列引數的機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-667">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8739b-668">引數</span><span class="sxs-lookup"><span data-stu-id="8739b-668">Arguments</span></span>

<span data-ttu-id="8739b-669">當值後面接著空格時，值必須接著等號 (`=`)，或機碼必須有前置詞 (`--` 或 `/`)。</span><span class="sxs-lookup"><span data-stu-id="8739b-669">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8739b-670">如果使用等號 (例如 `CommandLineKey=`)，則不需要此值。</span><span class="sxs-lookup"><span data-stu-id="8739b-670">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8739b-671">索引鍵前置字元</span><span class="sxs-lookup"><span data-stu-id="8739b-671">Key prefix</span></span>               | <span data-ttu-id="8739b-672">範例</span><span class="sxs-lookup"><span data-stu-id="8739b-672">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8739b-673">沒有前置字元</span><span class="sxs-lookup"><span data-stu-id="8739b-673">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8739b-674">雙虛線 (`--`)</span><span class="sxs-lookup"><span data-stu-id="8739b-674">Two dashes (`--`)</span></span>        | <span data-ttu-id="8739b-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8739b-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8739b-676">正斜線 (`/`)</span><span class="sxs-lookup"><span data-stu-id="8739b-676">Forward slash (`/`)</span></span>      | <span data-ttu-id="8739b-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8739b-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8739b-678">在相同的命令中，請不要混合使用等號搭配使用空格之機碼值組的命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-678">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8739b-679">範例命令：</span><span class="sxs-lookup"><span data-stu-id="8739b-679">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8739b-680">切換對應</span><span class="sxs-lookup"><span data-stu-id="8739b-680">Switch mappings</span></span>

<span data-ttu-id="8739b-681">參數對應允許索引鍵名稱取代邏輯。</span><span class="sxs-lookup"><span data-stu-id="8739b-681">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8739b-682">使用手動建立設定時 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> ，請提供方法的切換替代字典 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 。</span><span class="sxs-lookup"><span data-stu-id="8739b-682">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8739b-683">使用切換對應字典時，會檢查字典中是否有任何索引鍵符合命令列引數所提供的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="8739b-683">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8739b-684">如果在字典中找到命令列索引鍵，則會傳回字典值 (索引鍵取代) 以在應用程式的設定中設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-684">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8739b-685">所有前面加上單虛線 (`-`) 的命令列索引鍵都需要切換對應。</span><span class="sxs-lookup"><span data-stu-id="8739b-685">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8739b-686">切換對應字典索引鍵規則：</span><span class="sxs-lookup"><span data-stu-id="8739b-686">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8739b-687">切換必須以單虛線 (`-`) 或雙虛線 (`--`) 開頭。</span><span class="sxs-lookup"><span data-stu-id="8739b-687">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8739b-688">切換對應字典不能包含重複索引鍵。</span><span class="sxs-lookup"><span data-stu-id="8739b-688">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8739b-689">建立切換對應字典。</span><span class="sxs-lookup"><span data-stu-id="8739b-689">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8739b-690">下列範例會建立兩個切換對應：</span><span class="sxs-lookup"><span data-stu-id="8739b-690">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8739b-691">建立主機時，請使用切換對應字典呼叫 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="8739b-691">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8739b-692">針對使用切換對應的應用程式，呼叫 `CreateDefaultBuilder` 不應傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-692">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8739b-693">`CreateDefaultBuilder` 方法的 `AddCommandLine` 呼叫不包括對應的切換，且沒有任何方式可以將切換對應字典傳遞給 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8739b-693">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739b-694">解決方案並非將引數傳遞給 `CreateDefaultBuilder`，而是允許 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法同時處理引數與切換對應字典。</span><span class="sxs-lookup"><span data-stu-id="8739b-694">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8739b-695">建立切換對應字典之後，它會包含下表中所示的資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-695">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8739b-696">Key</span><span class="sxs-lookup"><span data-stu-id="8739b-696">Key</span></span>       | <span data-ttu-id="8739b-697">值</span><span class="sxs-lookup"><span data-stu-id="8739b-697">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8739b-698">若啟動應用程式時使用切換對應機碼，設定會接收由字典提供之機碼上的設定值：</span><span class="sxs-lookup"><span data-stu-id="8739b-698">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8739b-699">執行上述命令之後，設定包含下表中顯示的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-699">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8739b-700">Key</span><span class="sxs-lookup"><span data-stu-id="8739b-700">Key</span></span>               | <span data-ttu-id="8739b-701">值</span><span class="sxs-lookup"><span data-stu-id="8739b-701">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8739b-702">環境變數設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-702">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8739b-703"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 會在執行階段從環境變數機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-703">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8739b-704">若要啟用環境變數設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-704">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8739b-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) 允許在 Azure 入口網站中設定可使用環境變數設定提供者覆寫應用程式設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8739b-706">如需詳細資訊，請參閱 [Azure App：使用 Azure 入口網站覆寫應用程式設定](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="8739b-706">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8739b-707">使用 [Web 主機](xref:fundamentals/host/web-host)初始化新的主機建立器並呼叫 `CreateDefaultBuilder` 時，可使用 `AddEnvironmentVariables` 為[主機組態](#host-versus-app-configuration)載入字首為 `ASPNETCORE_` 的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-707">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8739b-708">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="8739b-708">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739b-709">`CreateDefaultBuilder` 也會載入：</span><span class="sxs-lookup"><span data-stu-id="8739b-709">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8739b-710">來自無首碼之環境變數的應用程式組態 (在未提供首碼的情況下呼叫 `AddEnvironmentVariables`)。</span><span class="sxs-lookup"><span data-stu-id="8739b-710">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8739b-711">自和 appsettings 的選擇性設定 *appsettings.json* *。環境}. json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-711">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8739b-712">開發環境中的[使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8739b-712">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739b-713">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-713">Command-line arguments.</span></span>

<span data-ttu-id="8739b-714">從使用者祕密與 *appsettings* 檔案建立設定之後，會呼叫「環境變數設定提供者」。</span><span class="sxs-lookup"><span data-stu-id="8739b-714">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8739b-715">在此位置呼叫提供者可讓系統在執行階段讀取環境變數，以覆寫由使用者祕密與 *appsettings* 檔案所設定的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-715">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8739b-716">若要從其他環境變數提供應用程式設定，請在中呼叫應用程式的其他提供者， `ConfigureAppConfiguration` 並使用前置詞來呼叫 `AddEnvironmentVariables` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-716">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8739b-717">呼叫 `AddEnvironmentVariables` last 以允許具有指定前置詞的環境變數覆寫其他提供者的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-717">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8739b-718">**範例**</span><span class="sxs-lookup"><span data-stu-id="8739b-718">**Example**</span></span>

<span data-ttu-id="8739b-719">範例應用程式利用靜態方便方法 `CreateDefaultBuilder` 的優勢來建置主機，這包括對 `AddEnvironmentVariables` 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="8739b-719">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8739b-720">執行範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-720">Run the sample app.</span></span> <span data-ttu-id="8739b-721">開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-721">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8739b-722">觀察輸出是否包含環境變數 `ENVIRONMENT` 的機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-722">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8739b-723">值反映應用程式執行所在環境，在本機執行時，通常是 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8739b-723">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8739b-724">為縮短由應用程式轉譯的環境變數清單，應用程式會篩選環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-724">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8739b-725">請參閱範例應用程式的 *Pages/Index.cshtml.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-725">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8739b-726">若要將所有可用的環境變數公開給應用程式，請 `FilteredConfiguration` 將 *Pages/Index. .cs* 變更為下列內容：</span><span class="sxs-lookup"><span data-stu-id="8739b-726">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8739b-727">首碼</span><span class="sxs-lookup"><span data-stu-id="8739b-727">Prefixes</span></span>

<span data-ttu-id="8739b-728">在提供方法的前置詞時，會篩選載入至應用程式設定的環境變數 `AddEnvironmentVariables` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-728">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8739b-729">例如，若要篩選前置詞為 `CUSTOM_` 的環境變數，請提供前置詞給設定提供者：</span><span class="sxs-lookup"><span data-stu-id="8739b-729">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8739b-730">建立設定機碼值組時，會移除前置詞。</span><span class="sxs-lookup"><span data-stu-id="8739b-730">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8739b-731">建立主機建立器時，主機組態由環境變數提供。</span><span class="sxs-lookup"><span data-stu-id="8739b-731">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8739b-732">如需這些環境變數所用前置詞的詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="8739b-732">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739b-733">**連接字串前置詞**</span><span class="sxs-lookup"><span data-stu-id="8739b-733">**Connection string prefixes**</span></span>

<span data-ttu-id="8739b-734">設定 API 四個連接字串環境變數的特殊處理規則，這些這些環境變數牽涉到針對應用程式環境設定 Azure 連接字串。</span><span class="sxs-lookup"><span data-stu-id="8739b-734">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8739b-735">若將前置詞提供給 `AddEnvironmentVariables`具有下表顯示之前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-735">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8739b-736">連接字串前置詞</span><span class="sxs-lookup"><span data-stu-id="8739b-736">Connection string prefix</span></span> | <span data-ttu-id="8739b-737">提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-737">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8739b-738">自訂提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-738">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8739b-739">MySQL</span><span class="sxs-lookup"><span data-stu-id="8739b-739">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8739b-740">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8739b-740">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8739b-741">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8739b-741">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8739b-742">當探索到具有下表所顯示之任何四個前置詞的環境變數並將其載入到設定中時：</span><span class="sxs-lookup"><span data-stu-id="8739b-742">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8739b-743">會透過移除環境變數前置詞並新增設定機碼區段 (`ConnectionStrings`) 來移除具有下表顯示之前置詞的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-743">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8739b-744">會建立新的設定機碼值組以代表資料庫連線提供者 (`CUSTOMCONNSTR_` 除外，這沒有所述提供者)。</span><span class="sxs-lookup"><span data-stu-id="8739b-744">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8739b-745">環境變數機碼</span><span class="sxs-lookup"><span data-stu-id="8739b-745">Environment variable key</span></span> | <span data-ttu-id="8739b-746">已轉換的設定機碼</span><span class="sxs-lookup"><span data-stu-id="8739b-746">Converted configuration key</span></span> | <span data-ttu-id="8739b-747">提供者設定項目</span><span class="sxs-lookup"><span data-stu-id="8739b-747">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-748">設定項目未建立。</span><span class="sxs-lookup"><span data-stu-id="8739b-748">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-749">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8739b-749">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739b-750">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739b-750">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-751">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8739b-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739b-752">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739b-752">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739b-753">機碼：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8739b-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739b-754">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739b-754">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="8739b-755">**範例**</span><span class="sxs-lookup"><span data-stu-id="8739b-755">**Example**</span></span>

<span data-ttu-id="8739b-756">在伺服器上建立自訂連接字串環境變數：</span><span class="sxs-lookup"><span data-stu-id="8739b-756">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8739b-757">名稱：`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8739b-757">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8739b-758">值：`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8739b-758">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8739b-759">如果 `IConfiguration` 插入並指派給名為的欄位 `_config` ，請閱讀下列值：</span><span class="sxs-lookup"><span data-stu-id="8739b-759">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8739b-760">檔案設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-760">File Configuration Provider</span></span>

<span data-ttu-id="8739b-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是用於從檔案系統載入設定的基底類別。</span><span class="sxs-lookup"><span data-stu-id="8739b-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8739b-762">下列設定提供者專用於特定檔案類型：</span><span class="sxs-lookup"><span data-stu-id="8739b-762">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8739b-763">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-763">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8739b-764">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-764">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8739b-765">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-765">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8739b-766">INI 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-766">INI Configuration Provider</span></span>

<span data-ttu-id="8739b-767"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 會在執行階段從 INI 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-767">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739b-768">若要啟用 INI 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-768">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739b-769">冒號可用來做為 INI 檔案設定中的區段分隔符號。</span><span class="sxs-lookup"><span data-stu-id="8739b-769">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8739b-770">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="8739b-770">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739b-771">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="8739b-771">Whether the file is optional.</span></span>
* <span data-ttu-id="8739b-772">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-772">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8739b-773"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-773">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8739b-774">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8739b-775">INI 設定檔的一般範例：</span><span class="sxs-lookup"><span data-stu-id="8739b-775">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8739b-776">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-776">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739b-777">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-777">section0:key0</span></span>
* <span data-ttu-id="8739b-778">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-778">section0:key1</span></span>
* <span data-ttu-id="8739b-779">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8739b-779">section1:subsection:key</span></span>
* <span data-ttu-id="8739b-780">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8739b-780">section2:subsection0:key</span></span>
* <span data-ttu-id="8739b-781">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8739b-781">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8739b-782">JSON 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-782">JSON Configuration Provider</span></span>

<span data-ttu-id="8739b-783"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 會在執行階段從 JSON 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-783">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8739b-784">若要啟用 JSON 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-784">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739b-785">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="8739b-785">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739b-786">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="8739b-786">Whether the file is optional.</span></span>
* <span data-ttu-id="8739b-787">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-787">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8739b-788"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-788">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8739b-789">`AddJsonFile` 使用初始化新的主機建立器時，會自動呼叫兩次 `CreateDefaultBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-789">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739b-790">會呼叫此方法以從下列位置載入設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-790">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8739b-791">*appsettings.json*：先讀取此檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-791">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="8739b-792">檔案的環境版本可以覆寫檔案所提供的值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-792">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8739b-793">*appsettings。{環境}. json*：檔案的環境版本是根據 [IHostingEnvironment EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)載入。</span><span class="sxs-lookup"><span data-stu-id="8739b-793">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8739b-794">如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="8739b-794">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739b-795">`CreateDefaultBuilder` 也會載入：</span><span class="sxs-lookup"><span data-stu-id="8739b-795">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8739b-796">環境變數。</span><span class="sxs-lookup"><span data-stu-id="8739b-796">Environment variables.</span></span>
* <span data-ttu-id="8739b-797">開發環境中的[使用者秘密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8739b-797">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739b-798">命令列引數。</span><span class="sxs-lookup"><span data-stu-id="8739b-798">Command-line arguments.</span></span>

<span data-ttu-id="8739b-799">會先建立 JSON 設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-799">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8739b-800">因此，使用者祕密、環境變數與命令列引數會覆寫由 *appsettings* 檔案設定的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-800">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8739b-801">`ConfigureAppConfiguration`建立主機以針對和 appsettings 以外的檔案指定應用程式的設定時，請呼叫 *appsettings.json* *。 {環境}. json*：</span><span class="sxs-lookup"><span data-stu-id="8739b-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8739b-802">**範例**</span><span class="sxs-lookup"><span data-stu-id="8739b-802">**Example**</span></span>

<span data-ttu-id="8739b-803">範例應用程式會利用靜態的便利性方法 `CreateDefaultBuilder` 來建立主機，其中包含兩個對的呼叫 `AddJsonFile` ：</span><span class="sxs-lookup"><span data-stu-id="8739b-803">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8739b-804">從下列載入設定的第一個呼叫 `AddJsonFile` *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="8739b-804">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8739b-805">從 appsettings 載入設定的第二個呼叫 `AddJsonFile` *。 {環境}. json*。</span><span class="sxs-lookup"><span data-stu-id="8739b-805">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8739b-806">針對範例應用程式中的 *appsettings.Development.js* ，會載入下列檔案：</span><span class="sxs-lookup"><span data-stu-id="8739b-806">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8739b-807">執行範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-807">Run the sample app.</span></span> <span data-ttu-id="8739b-808">開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-808">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8739b-809">輸出會根據應用程式的環境，包含設定的機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-809">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8739b-810">金鑰的記錄層級 `Logging:LogLevel:Default` 是在 `Debug` 開發環境中執行應用程式時。</span><span class="sxs-lookup"><span data-stu-id="8739b-810">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8739b-811">在生產環境中再次執行範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="8739b-811">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8739b-812">開啟檔案的 *屬性/launchSettings.js* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-812">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8739b-813">在 `ConfigurationSample` 設定檔中，將環境變數的值變更 `ASPNETCORE_ENVIRONMENT` 為 `Production` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-813">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8739b-814">使用命令介面儲存檔案，並 `dotnet run` 在中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="8739b-814">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8739b-815">*appsettings.Development.js* 中的設定不會再覆寫中的設定 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="8739b-815">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8739b-816">索引鍵的記錄層級 `Logging:LogLevel:Default` 為 `Warning` 。</span><span class="sxs-lookup"><span data-stu-id="8739b-816">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8739b-817">XML 設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-817">XML Configuration Provider</span></span>

<span data-ttu-id="8739b-818"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 會在執行階段從 XML 檔案機碼值組載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-818">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739b-819">若要啟用 XML 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-819">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739b-820">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="8739b-820">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739b-821">檔案是否為選擇性。</span><span class="sxs-lookup"><span data-stu-id="8739b-821">Whether the file is optional.</span></span>
* <span data-ttu-id="8739b-822">檔案變更時是否要重新載入設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-822">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8739b-823"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-823">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8739b-824">建立設定機碼值組時，會忽略設定檔案的根節點。</span><span class="sxs-lookup"><span data-stu-id="8739b-824">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8739b-825">請勿在檔案中指定文件類型定義 (DTD) 或命名空間。</span><span class="sxs-lookup"><span data-stu-id="8739b-825">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8739b-826">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-826">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8739b-827">XML 設定檔可以針對重複的區段使用相異元素名稱：</span><span class="sxs-lookup"><span data-stu-id="8739b-827">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8739b-828">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-828">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739b-829">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-829">section0:key0</span></span>
* <span data-ttu-id="8739b-830">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-830">section0:key1</span></span>
* <span data-ttu-id="8739b-831">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-831">section1:key0</span></span>
* <span data-ttu-id="8739b-832">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-832">section1:key1</span></span>

<span data-ttu-id="8739b-833">若 `name` 屬性是用來區別元素，則可以重複那些使用相同元素名稱的元素：</span><span class="sxs-lookup"><span data-stu-id="8739b-833">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8739b-834">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-834">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739b-835">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-835">section:section0:key:key0</span></span>
* <span data-ttu-id="8739b-836">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-836">section:section0:key:key1</span></span>
* <span data-ttu-id="8739b-837">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-837">section:section1:key:key0</span></span>
* <span data-ttu-id="8739b-838">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-838">section:section1:key:key1</span></span>

<span data-ttu-id="8739b-839">屬性可用來提供值：</span><span class="sxs-lookup"><span data-stu-id="8739b-839">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8739b-840">先前的設定檔會使用 `value` 載入下列機碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-840">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739b-841">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739b-841">key:attribute</span></span>
* <span data-ttu-id="8739b-842">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739b-842">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8739b-843">每個檔案機碼設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-843">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8739b-844"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目錄的檔案做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-844">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8739b-845">機碼是檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="8739b-845">The key is the file name.</span></span> <span data-ttu-id="8739b-846">值包含檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="8739b-846">The value contains the file's contents.</span></span> <span data-ttu-id="8739b-847">每個檔案機碼設定提供者是在 Docker 主機案例中使用。</span><span class="sxs-lookup"><span data-stu-id="8739b-847">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8739b-848">若要啟用每個檔案機碼設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-848">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8739b-849">檔案的 `directoryPath` 必須是絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="8739b-849">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8739b-850">多載允許指定：</span><span class="sxs-lookup"><span data-stu-id="8739b-850">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739b-851">設定來源的 `Action<KeyPerFileConfigurationSource>` 委派。</span><span class="sxs-lookup"><span data-stu-id="8739b-851">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8739b-852">目錄是否為選擇性與目錄的路徑。</span><span class="sxs-lookup"><span data-stu-id="8739b-852">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8739b-853">雙底線 (`__`) 是做為檔案名稱中的設定金鑰分隔符號使用。</span><span class="sxs-lookup"><span data-stu-id="8739b-853">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8739b-854">例如，檔案名稱 `Logging__LogLevel__System` 會產生設定金鑰 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="8739b-854">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8739b-855">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：</span><span class="sxs-lookup"><span data-stu-id="8739b-855">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8739b-856">記憶體設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-856">Memory Configuration Provider</span></span>

<span data-ttu-id="8739b-857"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用記憶體內集合做為設定機碼值組。</span><span class="sxs-lookup"><span data-stu-id="8739b-857">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8739b-858">若要啟用記憶體內集合設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 延伸模組方法。</span><span class="sxs-lookup"><span data-stu-id="8739b-858">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739b-859">設定提供者可以使用 `IEnumerable<KeyValuePair<String,String>>` 來初始化。</span><span class="sxs-lookup"><span data-stu-id="8739b-859">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8739b-860">建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="8739b-860">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8739b-861">下列範例會建立組態字典：</span><span class="sxs-lookup"><span data-stu-id="8739b-861">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8739b-862">字典會與 `AddInMemoryCollection` 的呼叫搭配使用以提供組態：</span><span class="sxs-lookup"><span data-stu-id="8739b-862">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8739b-863">GetValue</span><span class="sxs-lookup"><span data-stu-id="8739b-863">GetValue</span></span>

<span data-ttu-id="8739b-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 從具有指定索引鍵的設定中解壓縮單一值，並將其轉換為指定的 noncollection 類型。</span><span class="sxs-lookup"><span data-stu-id="8739b-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8739b-865">多載接受預設值。</span><span class="sxs-lookup"><span data-stu-id="8739b-865">An overload accepts a default value.</span></span>

<span data-ttu-id="8739b-866">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="8739b-866">The following example:</span></span>

* <span data-ttu-id="8739b-867">從具有機碼 `NumberKey` 的組態擷取字串值。</span><span class="sxs-lookup"><span data-stu-id="8739b-867">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8739b-868">若在組態機碼中找不到 `NumberKey`，則會使用預設值 `99`。</span><span class="sxs-lookup"><span data-stu-id="8739b-868">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8739b-869">鍵入值為 `int`。</span><span class="sxs-lookup"><span data-stu-id="8739b-869">Types the value as an `int`.</span></span>
* <span data-ttu-id="8739b-870">在 `NumberConfig` 屬性中儲存值供頁面使用。</span><span class="sxs-lookup"><span data-stu-id="8739b-870">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8739b-871">GetSection、GetChildren 與 Exists</span><span class="sxs-lookup"><span data-stu-id="8739b-871">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8739b-872">針對下面的範例，請考慮下列 JSON 檔案。</span><span class="sxs-lookup"><span data-stu-id="8739b-872">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8739b-873">在兩個區段中找到四個機碼，其中一個包括子區段組：</span><span class="sxs-lookup"><span data-stu-id="8739b-873">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8739b-874">當檔案被讀入到設定之後，會建立下列唯一階層式機碼以存放設定值：</span><span class="sxs-lookup"><span data-stu-id="8739b-874">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8739b-875">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-875">section0:key0</span></span>
* <span data-ttu-id="8739b-876">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-876">section0:key1</span></span>
* <span data-ttu-id="8739b-877">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-877">section1:key0</span></span>
* <span data-ttu-id="8739b-878">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-878">section1:key1</span></span>
* <span data-ttu-id="8739b-879">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-879">section2:subsection0:key0</span></span>
* <span data-ttu-id="8739b-880">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-880">section2:subsection0:key1</span></span>
* <span data-ttu-id="8739b-881">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8739b-881">section2:subsection1:key0</span></span>
* <span data-ttu-id="8739b-882">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8739b-882">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8739b-883">GetSection</span><span class="sxs-lookup"><span data-stu-id="8739b-883">GetSection</span></span>

<span data-ttu-id="8739b-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 會擷取具有所指定子區段機碼的設定子區段。</span><span class="sxs-lookup"><span data-stu-id="8739b-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8739b-885">若要傳回 `section1` 中只包含一個機碼值組的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，請呼叫 `GetSection` 並提供區段名稱：</span><span class="sxs-lookup"><span data-stu-id="8739b-885">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8739b-886">`configSection` 沒有值，只有索引鍵和路徑。</span><span class="sxs-lookup"><span data-stu-id="8739b-886">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8739b-887">同樣地，若要取得 `section2:subsection0` 中之機碼的值，請呼叫 `GetSection` 並提供區段路徑：</span><span class="sxs-lookup"><span data-stu-id="8739b-887">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8739b-888">`GetSection` 絕不會傳回 `null`。</span><span class="sxs-lookup"><span data-stu-id="8739b-888">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8739b-889">若找不到相符的區段，會傳回空的 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="8739b-889">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8739b-890">當 `GetSection` 傳回相符區段時，未填入 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>。</span><span class="sxs-lookup"><span data-stu-id="8739b-890">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8739b-891">當區段存在時，會傳回 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 與 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>。</span><span class="sxs-lookup"><span data-stu-id="8739b-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8739b-892">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8739b-892">GetChildren</span></span>

<span data-ttu-id="8739b-893">對 `section2` 上之 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 的呼叫會取得包括下列項目的 `IEnumerable<IConfigurationSection>`：</span><span class="sxs-lookup"><span data-stu-id="8739b-893">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8739b-894">Exists</span><span class="sxs-lookup"><span data-stu-id="8739b-894">Exists</span></span>

<span data-ttu-id="8739b-895">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 來判斷設定區段是否存在：</span><span class="sxs-lookup"><span data-stu-id="8739b-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8739b-896">以範例資料為例， `sectionExists` 是 `false`，因未設定資料中沒有 `section2:subsection2` 區段。</span><span class="sxs-lookup"><span data-stu-id="8739b-896">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8739b-897">繫結至物件圖形</span><span class="sxs-lookup"><span data-stu-id="8739b-897">Bind to an object graph</span></span>

<span data-ttu-id="8739b-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以繫結整個 POCO 物件圖形。</span><span class="sxs-lookup"><span data-stu-id="8739b-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8739b-899">如同系結簡單的物件，只會系結公用讀取/寫入屬性。</span><span class="sxs-lookup"><span data-stu-id="8739b-899">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8739b-900">範例包含 `TvShow` 模型，其物件圖形包括 `Metadata` 與 `Actors` 類別 (*Models/TvShow.cs*)：</span><span class="sxs-lookup"><span data-stu-id="8739b-900">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8739b-901">範例應用程式有 *tvshow.xml* 檔案，其中包含設定資料：</span><span class="sxs-lookup"><span data-stu-id="8739b-901">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8739b-902">設定會被繫結到整個 `TvShow` 物件 (使用 `Bind` 方法)。</span><span class="sxs-lookup"><span data-stu-id="8739b-902">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8739b-903">已繫結的執行個體會被指派給屬性以用於轉譯：</span><span class="sxs-lookup"><span data-stu-id="8739b-903">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8739b-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 系結並傳回指定的型別。</span><span class="sxs-lookup"><span data-stu-id="8739b-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8739b-905">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="8739b-905">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8739b-906">下列程式碼說明如何搭配 `Get<T>` 上述範例使用：</span><span class="sxs-lookup"><span data-stu-id="8739b-906">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8739b-907">將陣列繫結到類別</span><span class="sxs-lookup"><span data-stu-id="8739b-907">Bind an array to a class</span></span>

<span data-ttu-id="8739b-908">範例應用程式示範此節中解釋的概念。</span><span class="sxs-lookup"><span data-stu-id="8739b-908">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8739b-909"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支援在設定機碼中使用陣列索引將陣列繫結到物件。</span><span class="sxs-lookup"><span data-stu-id="8739b-909">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739b-910">任何公開數位索引鍵區段 (、) 的陣列格式， `:0:` `:1:` &hellip; `:{n}:` 都能夠將陣列系結至 POCO 類別陣列。</span><span class="sxs-lookup"><span data-stu-id="8739b-910">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8739b-911">繫結是由慣例提供。</span><span class="sxs-lookup"><span data-stu-id="8739b-911">Binding is provided by convention.</span></span> <span data-ttu-id="8739b-912">自訂設定提供者不需要實作陣列繫結。</span><span class="sxs-lookup"><span data-stu-id="8739b-912">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8739b-913">**記憶體內陣列處理**</span><span class="sxs-lookup"><span data-stu-id="8739b-913">**In-memory array processing**</span></span>

<span data-ttu-id="8739b-914">考慮下表中顯示的設定機碼與值。</span><span class="sxs-lookup"><span data-stu-id="8739b-914">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8739b-915">Key</span><span class="sxs-lookup"><span data-stu-id="8739b-915">Key</span></span>             | <span data-ttu-id="8739b-916">值</span><span class="sxs-lookup"><span data-stu-id="8739b-916">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8739b-917">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="8739b-917">array:entries:0</span></span> | <span data-ttu-id="8739b-918">value0</span><span class="sxs-lookup"><span data-stu-id="8739b-918">value0</span></span> |
| <span data-ttu-id="8739b-919">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="8739b-919">array:entries:1</span></span> | <span data-ttu-id="8739b-920">value1</span><span class="sxs-lookup"><span data-stu-id="8739b-920">value1</span></span> |
| <span data-ttu-id="8739b-921">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="8739b-921">array:entries:2</span></span> | <span data-ttu-id="8739b-922">value2</span><span class="sxs-lookup"><span data-stu-id="8739b-922">value2</span></span> |
| <span data-ttu-id="8739b-923">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="8739b-923">array:entries:4</span></span> | <span data-ttu-id="8739b-924">value4</span><span class="sxs-lookup"><span data-stu-id="8739b-924">value4</span></span> |
| <span data-ttu-id="8739b-925">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="8739b-925">array:entries:5</span></span> | <span data-ttu-id="8739b-926">value5</span><span class="sxs-lookup"><span data-stu-id="8739b-926">value5</span></span> |

<span data-ttu-id="8739b-927">這些機碼與值是使用「記憶體設定提供者」在範例應用程式中載入：</span><span class="sxs-lookup"><span data-stu-id="8739b-927">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8739b-928">陣列會跳過索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-928">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8739b-929">設定繫結程式無法繫結 Null 值或在已繫結的物件中建立 Null 項目，這在示範繫結此陣列的結果到某物件的時候已經很清楚。</span><span class="sxs-lookup"><span data-stu-id="8739b-929">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8739b-930">在範例應用程式中，POCO 類別可用來存放繫結設定資料：</span><span class="sxs-lookup"><span data-stu-id="8739b-930">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8739b-931">設定資料已繫結到物件：</span><span class="sxs-lookup"><span data-stu-id="8739b-931">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8739b-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 也可以使用語法，以產生更精簡的程式碼：</span><span class="sxs-lookup"><span data-stu-id="8739b-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8739b-933">繫結物件 (`ArrayExample`的執行個體) 會從設定接收陣列資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-933">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8739b-934">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="8739b-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8739b-935">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="8739b-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8739b-936">0</span><span class="sxs-lookup"><span data-stu-id="8739b-936">0</span></span>                            | <span data-ttu-id="8739b-937">value0</span><span class="sxs-lookup"><span data-stu-id="8739b-937">value0</span></span>                       |
| <span data-ttu-id="8739b-938">1</span><span class="sxs-lookup"><span data-stu-id="8739b-938">1</span></span>                            | <span data-ttu-id="8739b-939">value1</span><span class="sxs-lookup"><span data-stu-id="8739b-939">value1</span></span>                       |
| <span data-ttu-id="8739b-940">2</span><span class="sxs-lookup"><span data-stu-id="8739b-940">2</span></span>                            | <span data-ttu-id="8739b-941">value2</span><span class="sxs-lookup"><span data-stu-id="8739b-941">value2</span></span>                       |
| <span data-ttu-id="8739b-942">3</span><span class="sxs-lookup"><span data-stu-id="8739b-942">3</span></span>                            | <span data-ttu-id="8739b-943">value4</span><span class="sxs-lookup"><span data-stu-id="8739b-943">value4</span></span>                       |
| <span data-ttu-id="8739b-944">4</span><span class="sxs-lookup"><span data-stu-id="8739b-944">4</span></span>                            | <span data-ttu-id="8739b-945">value5</span><span class="sxs-lookup"><span data-stu-id="8739b-945">value5</span></span>                       |

<span data-ttu-id="8739b-946">繫結物件中的索引 &num;3 存放 `array:4` 設定機碼與其 `value4` 的設定資料。</span><span class="sxs-lookup"><span data-stu-id="8739b-946">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8739b-947">當繫結包含陣列的設定資料時，設定機碼中的陣列索引只會用來列舉設定資料 (當建立物件時)。</span><span class="sxs-lookup"><span data-stu-id="8739b-947">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8739b-948">設定資料中不能保留 Null 值，當設定機碼中的陣列略過一或多個索引時，不會在繫結物件中建立 Null 值項目。</span><span class="sxs-lookup"><span data-stu-id="8739b-948">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8739b-949">由會在設定中產生正確機碼值組的任何設定提供者繫結到 `ArrayExample` 執行個體之前，可以提供索引 &num;3 缺少的設定項目。</span><span class="sxs-lookup"><span data-stu-id="8739b-949">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8739b-950">若範例包含具有缺少之機碼值組的額外 JSON 設定提供者，`ArrayExample.Entries` 會符合完整的設定陣列：</span><span class="sxs-lookup"><span data-stu-id="8739b-950">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8739b-951">*missing_value.json*：</span><span class="sxs-lookup"><span data-stu-id="8739b-951">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8739b-952">在 `ConfigureAppConfiguration` 中：</span><span class="sxs-lookup"><span data-stu-id="8739b-952">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8739b-953">表格中顯示的機碼值組會載入到設定中。</span><span class="sxs-lookup"><span data-stu-id="8739b-953">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8739b-954">Key</span><span class="sxs-lookup"><span data-stu-id="8739b-954">Key</span></span>             | <span data-ttu-id="8739b-955">值</span><span class="sxs-lookup"><span data-stu-id="8739b-955">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8739b-956">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="8739b-956">array:entries:3</span></span> | <span data-ttu-id="8739b-957">value3</span><span class="sxs-lookup"><span data-stu-id="8739b-957">value3</span></span> |

<span data-ttu-id="8739b-958">在「JSON 設定提供者」包含索引 &num;3 的項目之後，若繫結 `ArrayExample` 類別執行個體，`ArrayExample.Entries` 陣列會包括這些值：</span><span class="sxs-lookup"><span data-stu-id="8739b-958">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8739b-959">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="8739b-959">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8739b-960">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="8739b-960">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8739b-961">0</span><span class="sxs-lookup"><span data-stu-id="8739b-961">0</span></span>                            | <span data-ttu-id="8739b-962">value0</span><span class="sxs-lookup"><span data-stu-id="8739b-962">value0</span></span>                       |
| <span data-ttu-id="8739b-963">1</span><span class="sxs-lookup"><span data-stu-id="8739b-963">1</span></span>                            | <span data-ttu-id="8739b-964">value1</span><span class="sxs-lookup"><span data-stu-id="8739b-964">value1</span></span>                       |
| <span data-ttu-id="8739b-965">2</span><span class="sxs-lookup"><span data-stu-id="8739b-965">2</span></span>                            | <span data-ttu-id="8739b-966">value2</span><span class="sxs-lookup"><span data-stu-id="8739b-966">value2</span></span>                       |
| <span data-ttu-id="8739b-967">3</span><span class="sxs-lookup"><span data-stu-id="8739b-967">3</span></span>                            | <span data-ttu-id="8739b-968">value3</span><span class="sxs-lookup"><span data-stu-id="8739b-968">value3</span></span>                       |
| <span data-ttu-id="8739b-969">4</span><span class="sxs-lookup"><span data-stu-id="8739b-969">4</span></span>                            | <span data-ttu-id="8739b-970">value4</span><span class="sxs-lookup"><span data-stu-id="8739b-970">value4</span></span>                       |
| <span data-ttu-id="8739b-971">5</span><span class="sxs-lookup"><span data-stu-id="8739b-971">5</span></span>                            | <span data-ttu-id="8739b-972">value5</span><span class="sxs-lookup"><span data-stu-id="8739b-972">value5</span></span>                       |

<span data-ttu-id="8739b-973">**JSON 陣列處理**</span><span class="sxs-lookup"><span data-stu-id="8739b-973">**JSON array processing**</span></span>

<span data-ttu-id="8739b-974">若 JSON 檔案包含陣列，會為具有以零為基礎之區段索引的陣列元素建立設定機碼。</span><span class="sxs-lookup"><span data-stu-id="8739b-974">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8739b-975">在下列設定檔中，`subsection` 是陣列：</span><span class="sxs-lookup"><span data-stu-id="8739b-975">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8739b-976">「JSON 設定提供者」會將設定資料讀入到下列機碼值組：</span><span class="sxs-lookup"><span data-stu-id="8739b-976">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8739b-977">Key</span><span class="sxs-lookup"><span data-stu-id="8739b-977">Key</span></span>                     | <span data-ttu-id="8739b-978">值</span><span class="sxs-lookup"><span data-stu-id="8739b-978">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8739b-979">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8739b-979">json_array:key</span></span>          | <span data-ttu-id="8739b-980">valueA</span><span class="sxs-lookup"><span data-stu-id="8739b-980">valueA</span></span> |
| <span data-ttu-id="8739b-981">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="8739b-981">json_array:subsection:0</span></span> | <span data-ttu-id="8739b-982">valueB</span><span class="sxs-lookup"><span data-stu-id="8739b-982">valueB</span></span> |
| <span data-ttu-id="8739b-983">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8739b-983">json_array:subsection:1</span></span> | <span data-ttu-id="8739b-984">valueC</span><span class="sxs-lookup"><span data-stu-id="8739b-984">valueC</span></span> |
| <span data-ttu-id="8739b-985">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8739b-985">json_array:subsection:2</span></span> | <span data-ttu-id="8739b-986">valueD</span><span class="sxs-lookup"><span data-stu-id="8739b-986">valueD</span></span> |

<span data-ttu-id="8739b-987">在範例應用程式中，下列 POCO 類別可用來繫結設定機碼值組：</span><span class="sxs-lookup"><span data-stu-id="8739b-987">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8739b-988">在繫結之後，`JsonArrayExample.Key` 會存放值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="8739b-988">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8739b-989">子區段值會存放在 POCO 陣列屬性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="8739b-989">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8739b-990">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="8739b-990">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8739b-991">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="8739b-991">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8739b-992">0</span><span class="sxs-lookup"><span data-stu-id="8739b-992">0</span></span>                                   | <span data-ttu-id="8739b-993">valueB</span><span class="sxs-lookup"><span data-stu-id="8739b-993">valueB</span></span>                              |
| <span data-ttu-id="8739b-994">1</span><span class="sxs-lookup"><span data-stu-id="8739b-994">1</span></span>                                   | <span data-ttu-id="8739b-995">valueC</span><span class="sxs-lookup"><span data-stu-id="8739b-995">valueC</span></span>                              |
| <span data-ttu-id="8739b-996">2</span><span class="sxs-lookup"><span data-stu-id="8739b-996">2</span></span>                                   | <span data-ttu-id="8739b-997">valueD</span><span class="sxs-lookup"><span data-stu-id="8739b-997">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8739b-998">自訂設定提供者</span><span class="sxs-lookup"><span data-stu-id="8739b-998">Custom configuration provider</span></span>

<span data-ttu-id="8739b-999">範例應用程式示範如何建立會使用 [Entity Framework (EF)](/ef/core/) 從資料庫讀取設定機碼值組的基本設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-999">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8739b-1000">提供者具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="8739b-1000">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8739b-1001">EF 記憶體內資料庫會用於示範用途。</span><span class="sxs-lookup"><span data-stu-id="8739b-1001">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8739b-1002">若要使用需要連接字串的資料庫，請實作第二個 `ConfigurationBuilder` 以從另一個設定提供者提供連接字串。</span><span class="sxs-lookup"><span data-stu-id="8739b-1002">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8739b-1003">啟動時，該提供者會將資料庫資料表讀入到設定中。</span><span class="sxs-lookup"><span data-stu-id="8739b-1003">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8739b-1004">該提供者不會以個別機碼為基礎查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="8739b-1004">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8739b-1005">未實作變更時重新載入，因此在應用程式啟動後更新資料庫對應用程式設定沒有影響。</span><span class="sxs-lookup"><span data-stu-id="8739b-1005">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8739b-1006">定義 `EFConfigurationValue` 實體來在資料庫中存放設定值。</span><span class="sxs-lookup"><span data-stu-id="8739b-1006">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8739b-1007">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-1007">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8739b-1008">新增 `EFConfigurationContext` 以存放及存取已設定的值。</span><span class="sxs-lookup"><span data-stu-id="8739b-1008">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8739b-1009">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8739b-1010">建立會實作 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的類別。</span><span class="sxs-lookup"><span data-stu-id="8739b-1010">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8739b-1011">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8739b-1012">透過繼承自 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 來建立自訂設定提供者。</span><span class="sxs-lookup"><span data-stu-id="8739b-1012">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8739b-1013">若資料庫是空的，設定提供者會初始化資料庫。</span><span class="sxs-lookup"><span data-stu-id="8739b-1013">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8739b-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="8739b-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8739b-1015">`AddEFConfiguration` 擴充方法允許新增設定來源到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8739b-1015">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8739b-1016">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739b-1016">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8739b-1017">下列程式碼顯示如何在 *Program.cs* 中使用自訂 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="8739b-1017">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8739b-1018">在啟動期間存取組態</span><span class="sxs-lookup"><span data-stu-id="8739b-1018">Access configuration during startup</span></span>

<span data-ttu-id="8739b-1019">將 `IConfiguration` 插入到 `Startup` 建構函式，以存取 `Startup.ConfigureServices` 中的設定值。</span><span class="sxs-lookup"><span data-stu-id="8739b-1019">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8739b-1020">若要存取 `Startup.Configure` 中的設定，請直接將 `IConfiguration` 插入到方法或從建構函式使用執行個體：</span><span class="sxs-lookup"><span data-stu-id="8739b-1020">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8739b-1021">如需使用啟動方便方法來存取設定的範例，請參閱[應用程式啟動：方便方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="8739b-1021">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8739b-1022">存取 Razor 頁面頁面或 MVC 視圖中的設定</span><span class="sxs-lookup"><span data-stu-id="8739b-1022">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8739b-1023">若要存取 Razor 頁面頁面或 MVC 視圖中的設定設定，請新增 [using](xref:mvc/views/razor#using) 指示詞 ([c # 參考：使用](/dotnet/csharp/language-reference/keywords/using-directive) [Microsoft.Extensions.Configuration 命名空間](xref:Microsoft.Extensions.Configuration) 的指示詞) ，然後插入 <xref:Microsoft.Extensions.Configuration.IConfiguration> 頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="8739b-1023">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8739b-1024">在 [ Razor 頁面] 頁面中：</span><span class="sxs-lookup"><span data-stu-id="8739b-1024">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8739b-1025">在 MVC 檢視中：</span><span class="sxs-lookup"><span data-stu-id="8739b-1025">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8739b-1026">從外部組件新增設定</span><span class="sxs-lookup"><span data-stu-id="8739b-1026">Add configuration from an external assembly</span></span>

<span data-ttu-id="8739b-1027"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 實作允許在啟動時從應用程式 `Startup` 類別外部的外部組件，針對應用程式新增增強功能。</span><span class="sxs-lookup"><span data-stu-id="8739b-1027">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8739b-1028">如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8739b-1028">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8739b-1029">其他資源</span><span class="sxs-lookup"><span data-stu-id="8739b-1029">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
