---
title: 在 ASP.NET Core 的開發中安全儲存應用程式秘密
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式開發期間儲存和取得機密資訊。
ms.author: scaddie
ms.custom: mvc, contperfq2
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
uid: security/app-secrets
ms.openlocfilehash: 99b7b04076206f95c04da79283010beafdd1cc88
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035849"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="be7d8-103">在 ASP.NET Core 的開發中安全儲存應用程式秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be7d8-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)及 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="be7d8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="be7d8-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="be7d8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="be7d8-106">本檔說明如何管理開發電腦上 ASP.NET Core 應用程式的敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="be7d8-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="be7d8-107">絕對不要將密碼或其他敏感性資料儲存在原始程式碼中。</span><span class="sxs-lookup"><span data-stu-id="be7d8-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="be7d8-108">生產秘密不應用於開發或測試。</span><span class="sxs-lookup"><span data-stu-id="be7d8-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="be7d8-109">秘密不應與應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="be7d8-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="be7d8-110">相反地，您應該透過像環境變數或 Azure Key Vault 一樣的控制方式來存取生產秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="be7d8-111">您可以透過 [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)儲存及保護 Azure 測試與生產祕密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="be7d8-112">環境變數</span><span class="sxs-lookup"><span data-stu-id="be7d8-112">Environment variables</span></span>

<span data-ttu-id="be7d8-113">環境變數是用來避免在程式碼或本機設定檔案中儲存應用程式秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="be7d8-114">環境變數會覆寫所有先前指定設定來源的設定值。</span><span class="sxs-lookup"><span data-stu-id="be7d8-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="be7d8-115">請考慮啟用 **個別使用者帳戶** 安全性的 ASP.NET Core web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="be7d8-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="be7d8-116">具有該索引鍵的專案檔案中包含預設的資料庫連接字串 *appsettings.json* `DefaultConnection` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="be7d8-117">預設連接字串適用于 LocalDB，以使用者模式執行，且不需要密碼。</span><span class="sxs-lookup"><span data-stu-id="be7d8-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="be7d8-118">在應用程式部署期間， `DefaultConnection` 可以使用環境變數的值來覆寫金鑰值。</span><span class="sxs-lookup"><span data-stu-id="be7d8-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="be7d8-119">環境變數可以儲存具有敏感性認證的完整連接字串。</span><span class="sxs-lookup"><span data-stu-id="be7d8-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="be7d8-120">環境變數通常會以純文字未加密的文字儲存。</span><span class="sxs-lookup"><span data-stu-id="be7d8-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="be7d8-121">如果電腦或進程遭到入侵，則不受信任的合作物件可以存取環境變數。</span><span class="sxs-lookup"><span data-stu-id="be7d8-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="be7d8-122">可能需要其他措施來防止洩漏使用者秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="be7d8-123">秘密管理員</span><span class="sxs-lookup"><span data-stu-id="be7d8-123">Secret Manager</span></span>

<span data-ttu-id="be7d8-124">在 ASP.NET Core 專案的開發期間，秘密管理員工具會儲存機密資料。</span><span class="sxs-lookup"><span data-stu-id="be7d8-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="be7d8-125">在此內容中，有一段機密資料是應用程式秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="be7d8-126">應用程式秘密會儲存在專案樹狀結構中的不同位置。</span><span class="sxs-lookup"><span data-stu-id="be7d8-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="be7d8-127">應用程式秘密會與特定專案建立關聯，或在數個專案之間共用。</span><span class="sxs-lookup"><span data-stu-id="be7d8-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="be7d8-128">應用程式秘密不會簽入原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="be7d8-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="be7d8-129">秘密管理員工具不會加密儲存的密碼，也不應將其視為受信任的存放區。</span><span class="sxs-lookup"><span data-stu-id="be7d8-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="be7d8-130">這僅適用于開發用途。</span><span class="sxs-lookup"><span data-stu-id="be7d8-130">It's for development purposes only.</span></span> <span data-ttu-id="be7d8-131">金鑰和值會儲存在使用者設定檔目錄的 JSON 設定檔中。</span><span class="sxs-lookup"><span data-stu-id="be7d8-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="be7d8-132">秘密管理員工具的運作方式</span><span class="sxs-lookup"><span data-stu-id="be7d8-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="be7d8-133">秘密管理員工具會隱藏執行詳細資料，例如儲存值的位置和方式。</span><span class="sxs-lookup"><span data-stu-id="be7d8-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="be7d8-134">您可以使用此工具，而不需要瞭解這些實作為詳細資料。</span><span class="sxs-lookup"><span data-stu-id="be7d8-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="be7d8-135">這些值會儲存在本機電腦的使用者設定檔資料夾中的 JSON 檔案中：</span><span class="sxs-lookup"><span data-stu-id="be7d8-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="be7d8-136">Windows</span><span class="sxs-lookup"><span data-stu-id="be7d8-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="be7d8-137">檔案系統路徑：</span><span class="sxs-lookup"><span data-stu-id="be7d8-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="be7d8-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="be7d8-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="be7d8-139">檔案系統路徑：</span><span class="sxs-lookup"><span data-stu-id="be7d8-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="be7d8-140">在先前的檔案路徑中，以 `<user_secrets_id>` 專案檔中所 `UserSecretsId` 指定的值取代。</span><span class="sxs-lookup"><span data-stu-id="be7d8-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="be7d8-141">請勿撰寫相依于使用 Secret Manager 工具儲存的資料位置或格式的程式碼。</span><span class="sxs-lookup"><span data-stu-id="be7d8-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="be7d8-142">這些執行詳細資料可能會變更。</span><span class="sxs-lookup"><span data-stu-id="be7d8-142">These implementation details may change.</span></span> <span data-ttu-id="be7d8-143">例如，秘密值不會加密，但未來可能是如此。</span><span class="sxs-lookup"><span data-stu-id="be7d8-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="be7d8-144">啟用秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="be7d8-144">Enable secret storage</span></span>

<span data-ttu-id="be7d8-145">秘密管理員工具會操作儲存在使用者設定檔中的專案特定設定。</span><span class="sxs-lookup"><span data-stu-id="be7d8-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="be7d8-146">秘密管理員工具組含 `init` .NET Core SDK 3.0.100 或更新版本中的命令。</span><span class="sxs-lookup"><span data-stu-id="be7d8-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="be7d8-147">若要使用使用者密碼，請在專案目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="be7d8-148">上述命令會 `UserSecretsId` 在專案檔的中加入 `PropertyGroup` 專案。</span><span class="sxs-lookup"><span data-stu-id="be7d8-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="be7d8-149">根據預設，的內部文字 `UserSecretsId` 為 GUID。</span><span class="sxs-lookup"><span data-stu-id="be7d8-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="be7d8-150">內部文字是任意的，但對專案而言是唯一的。</span><span class="sxs-lookup"><span data-stu-id="be7d8-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="be7d8-151">在 Visual Studio 中，以滑鼠右鍵按一下方案總管中的專案，然後從內容功能表中選取 [ **管理使用者密碼** ]。</span><span class="sxs-lookup"><span data-stu-id="be7d8-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="be7d8-152">這項手勢會將以 `UserSecretsId` GUID 填入的專案加入至專案檔。</span><span class="sxs-lookup"><span data-stu-id="be7d8-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="be7d8-153">設定秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-153">Set a secret</span></span>

<span data-ttu-id="be7d8-154">定義由機碼及其值組成的應用程式密碼。</span><span class="sxs-lookup"><span data-stu-id="be7d8-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="be7d8-155">此密碼與專案的值相關聯 `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="be7d8-156">例如，從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="be7d8-157">在上述範例中，冒號表示 `Movies` 為具有屬性的物件常值 `ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="be7d8-158">秘密管理員工具也可以從其他目錄使用。</span><span class="sxs-lookup"><span data-stu-id="be7d8-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="be7d8-159">您 `--project` 可以使用選項來提供專案檔所在的檔案系統路徑。</span><span class="sxs-lookup"><span data-stu-id="be7d8-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="be7d8-160">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="be7d8-161">Visual Studio 中的 JSON 結構壓平合併</span><span class="sxs-lookup"><span data-stu-id="be7d8-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="be7d8-162">Visual Studio 的 [ **管理使用者密碼** ] 手勢會在文字編輯器中開啟檔案的 *secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="be7d8-163">以要儲存的索引鍵/值組取代 *secrets.js* 的內容。</span><span class="sxs-lookup"><span data-stu-id="be7d8-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="be7d8-164">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="be7d8-165">JSON 結構會在透過或修改之後進行壓平合併 `dotnet user-secrets remove` `dotnet user-secrets set` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="be7d8-166">例如，執行會折迭 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 物件常值。</span><span class="sxs-lookup"><span data-stu-id="be7d8-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="be7d8-167">修改過的檔案類似下列 JSON：</span><span class="sxs-lookup"><span data-stu-id="be7d8-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="be7d8-168">設定多個秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-168">Set multiple secrets</span></span>

<span data-ttu-id="be7d8-169">您可以使用管線將 JSON 傳送至命令來設定一批秘密 `set` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="be7d8-170">在下列範例中，檔案的內容 *input.js* 會以管道傳送至 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="be7d8-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="be7d8-171">Windows</span><span class="sxs-lookup"><span data-stu-id="be7d8-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="be7d8-172">開啟命令 shell，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="be7d8-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="be7d8-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="be7d8-174">開啟命令 shell，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="be7d8-175">存取秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-175">Access a secret</span></span>

<span data-ttu-id="be7d8-176">若要存取秘密，請完成下列步驟：</span><span class="sxs-lookup"><span data-stu-id="be7d8-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="be7d8-177">註冊使用者秘密設定來源</span><span class="sxs-lookup"><span data-stu-id="be7d8-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="be7d8-178">透過設定 API 讀取秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="be7d8-179">註冊使用者秘密設定來源</span><span class="sxs-lookup"><span data-stu-id="be7d8-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="be7d8-180">使用者密碼設定 [提供者](/dotnet/core/extensions/configuration-providers) 會使用 .NET 設定 [API](xref:fundamentals/configuration/index)來註冊適當的設定來源。</span><span class="sxs-lookup"><span data-stu-id="be7d8-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="be7d8-181">當專案呼叫時，使用者秘密設定來源會自動加入至開發模式 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="be7d8-182">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>當為時 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> 呼叫 <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="be7d8-183">`CreateDefaultBuilder`未呼叫時，請在中呼叫以明確地新增使用者秘密設定來源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="be7d8-184">`AddUserSecrets`只有當應用程式在開發環境中執行時才呼叫，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="be7d8-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="be7d8-185">透過設定 API 讀取秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="be7d8-186">如果已登錄使用者秘密設定來源，則 .NET 設定 API 可以讀取秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="be7d8-187">您可以使用「函式[插入](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)」來取得 .NET 設定 API 的存取權。</span><span class="sxs-lookup"><span data-stu-id="be7d8-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="be7d8-188">請考慮下列讀取金鑰的範例 `Movies:ServiceApiKey` ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="be7d8-189">**Startup 類別：**</span><span class="sxs-lookup"><span data-stu-id="be7d8-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="be7d8-190">**Razor 頁面頁面模型：**</span><span class="sxs-lookup"><span data-stu-id="be7d8-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="be7d8-191">如需詳細資訊，請參閱 [啟動及[存取設定] Razor 頁面中](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)的[存取](xref:fundamentals/configuration/index#access-configuration-in-startup)設定。</span><span class="sxs-lookup"><span data-stu-id="be7d8-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="be7d8-192">將秘密對應至 POCO</span><span class="sxs-lookup"><span data-stu-id="be7d8-192">Map secrets to a POCO</span></span>

<span data-ttu-id="be7d8-193">將整個物件常值對應至 POCO (具有屬性) 的簡單 .NET 類別適用于匯總相關的屬性。</span><span class="sxs-lookup"><span data-stu-id="be7d8-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-194">若要將上述秘密對應至 POCO，請使用 .NET 設定 API 的 [物件圖形](xref:fundamentals/configuration/index#bind-to-an-object-graph) 系結功能。</span><span class="sxs-lookup"><span data-stu-id="be7d8-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="be7d8-195">下列程式碼會系結至自訂 `MovieSettings` POCO 並存取 `ServiceApiKey` 屬性值：</span><span class="sxs-lookup"><span data-stu-id="be7d8-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="be7d8-196">`Movies:ConnectionString`和 `Movies:ServiceApiKey` 密碼會對應到中的個別屬性 `MovieSettings` ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="be7d8-197">使用秘密取代字串</span><span class="sxs-lookup"><span data-stu-id="be7d8-197">String replacement with secrets</span></span>

<span data-ttu-id="be7d8-198">以純文字儲存密碼並非安全的。</span><span class="sxs-lookup"><span data-stu-id="be7d8-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="be7d8-199">例如，儲存在中的資料庫連接字串 *appsettings.json* 可能包含指定使用者的密碼：</span><span class="sxs-lookup"><span data-stu-id="be7d8-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="be7d8-200">更安全的方法是將密碼儲存為秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="be7d8-201">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="be7d8-202">`Password`從的連接字串中移除機碼值組 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="be7d8-203">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="be7d8-204">您可以在物件的屬性上設定秘密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成連接字串：</span><span class="sxs-lookup"><span data-stu-id="be7d8-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="be7d8-205">列出秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-206">從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="be7d8-207">下列輸出會出現：</span><span class="sxs-lookup"><span data-stu-id="be7d8-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="be7d8-208">在上述範例中，索引鍵名稱中的冒號代表 *secrets.js* 中的物件階層。</span><span class="sxs-lookup"><span data-stu-id="be7d8-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="be7d8-209">移除單一秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-210">從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="be7d8-211">應用程式 *在檔案上的secrets.js* 已修改為移除與機碼相關聯的索引鍵/值組 `MoviesConnectionString` ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="be7d8-212">`dotnet user-secrets list` 顯示下列訊息：</span><span class="sxs-lookup"><span data-stu-id="be7d8-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="be7d8-213">移除所有秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-214">從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="be7d8-215">應用程式的所有使用者密碼都已從 *secrets.js* 檔案中刪除：</span><span class="sxs-lookup"><span data-stu-id="be7d8-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="be7d8-216">`dotnet user-secrets list`執行會顯示下列訊息：</span><span class="sxs-lookup"><span data-stu-id="be7d8-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="be7d8-217">其他資源</span><span class="sxs-lookup"><span data-stu-id="be7d8-217">Additional resources</span></span>

* <span data-ttu-id="be7d8-218">請參閱 [這個問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) ，以取得從 IIS 存取使用者密碼的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="be7d8-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be7d8-219">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)及 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="be7d8-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="be7d8-220">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="be7d8-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="be7d8-221">本檔說明如何管理開發電腦上 ASP.NET Core 應用程式的敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="be7d8-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="be7d8-222">絕對不要將密碼或其他敏感性資料儲存在原始程式碼中。</span><span class="sxs-lookup"><span data-stu-id="be7d8-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="be7d8-223">生產秘密不應用於開發或測試。</span><span class="sxs-lookup"><span data-stu-id="be7d8-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="be7d8-224">秘密不應與應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="be7d8-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="be7d8-225">相反地，您應該透過像環境變數或 Azure Key Vault 一樣的控制方式來存取生產秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="be7d8-226">您可以透過 [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)儲存及保護 Azure 測試與生產祕密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="be7d8-227">環境變數</span><span class="sxs-lookup"><span data-stu-id="be7d8-227">Environment variables</span></span>

<span data-ttu-id="be7d8-228">環境變數是用來避免在程式碼或本機設定檔案中儲存應用程式秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="be7d8-229">環境變數會覆寫所有先前指定設定來源的設定值。</span><span class="sxs-lookup"><span data-stu-id="be7d8-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="be7d8-230">請考慮啟用 **個別使用者帳戶** 安全性的 ASP.NET Core web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="be7d8-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="be7d8-231">具有該索引鍵的專案檔案中包含預設的資料庫連接字串 *appsettings.json* `DefaultConnection` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="be7d8-232">預設連接字串適用于 LocalDB，以使用者模式執行，且不需要密碼。</span><span class="sxs-lookup"><span data-stu-id="be7d8-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="be7d8-233">在應用程式部署期間， `DefaultConnection` 可以使用環境變數的值來覆寫金鑰值。</span><span class="sxs-lookup"><span data-stu-id="be7d8-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="be7d8-234">環境變數可以儲存具有敏感性認證的完整連接字串。</span><span class="sxs-lookup"><span data-stu-id="be7d8-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="be7d8-235">環境變數通常會以純文字未加密的文字儲存。</span><span class="sxs-lookup"><span data-stu-id="be7d8-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="be7d8-236">如果電腦或進程遭到入侵，則不受信任的合作物件可以存取環境變數。</span><span class="sxs-lookup"><span data-stu-id="be7d8-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="be7d8-237">可能需要其他措施來防止洩漏使用者秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="be7d8-238">秘密管理員</span><span class="sxs-lookup"><span data-stu-id="be7d8-238">Secret Manager</span></span>

<span data-ttu-id="be7d8-239">在 ASP.NET Core 專案的開發期間，秘密管理員工具會儲存機密資料。</span><span class="sxs-lookup"><span data-stu-id="be7d8-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="be7d8-240">在此內容中，有一段機密資料是應用程式秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="be7d8-241">應用程式秘密會儲存在專案樹狀結構中的不同位置。</span><span class="sxs-lookup"><span data-stu-id="be7d8-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="be7d8-242">應用程式秘密會與特定專案建立關聯，或在數個專案之間共用。</span><span class="sxs-lookup"><span data-stu-id="be7d8-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="be7d8-243">應用程式秘密不會簽入原始檔控制中。</span><span class="sxs-lookup"><span data-stu-id="be7d8-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="be7d8-244">秘密管理員工具不會加密儲存的密碼，也不應將其視為受信任的存放區。</span><span class="sxs-lookup"><span data-stu-id="be7d8-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="be7d8-245">這僅適用于開發用途。</span><span class="sxs-lookup"><span data-stu-id="be7d8-245">It's for development purposes only.</span></span> <span data-ttu-id="be7d8-246">金鑰和值會儲存在使用者設定檔目錄的 JSON 設定檔中。</span><span class="sxs-lookup"><span data-stu-id="be7d8-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="be7d8-247">秘密管理員工具的運作方式</span><span class="sxs-lookup"><span data-stu-id="be7d8-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="be7d8-248">秘密管理員工具會隱藏執行詳細資料，例如儲存值的位置和方式。</span><span class="sxs-lookup"><span data-stu-id="be7d8-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="be7d8-249">您可以使用此工具，而不需要瞭解這些實作為詳細資料。</span><span class="sxs-lookup"><span data-stu-id="be7d8-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="be7d8-250">這些值會儲存在本機電腦的使用者設定檔資料夾中的 JSON 檔案中：</span><span class="sxs-lookup"><span data-stu-id="be7d8-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="be7d8-251">Windows</span><span class="sxs-lookup"><span data-stu-id="be7d8-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="be7d8-252">檔案系統路徑：</span><span class="sxs-lookup"><span data-stu-id="be7d8-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="be7d8-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="be7d8-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="be7d8-254">檔案系統路徑：</span><span class="sxs-lookup"><span data-stu-id="be7d8-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="be7d8-255">在先前的檔案路徑中，以 `<user_secrets_id>` 專案檔中所 `UserSecretsId` 指定的值取代。</span><span class="sxs-lookup"><span data-stu-id="be7d8-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="be7d8-256">請勿撰寫相依于使用 Secret Manager 工具儲存的資料位置或格式的程式碼。</span><span class="sxs-lookup"><span data-stu-id="be7d8-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="be7d8-257">這些執行詳細資料可能會變更。</span><span class="sxs-lookup"><span data-stu-id="be7d8-257">These implementation details may change.</span></span> <span data-ttu-id="be7d8-258">例如，秘密值不會加密，但未來可能是如此。</span><span class="sxs-lookup"><span data-stu-id="be7d8-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="be7d8-259">啟用秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="be7d8-259">Enable secret storage</span></span>

<span data-ttu-id="be7d8-260">秘密管理員工具會操作儲存在使用者設定檔中的專案特定設定。</span><span class="sxs-lookup"><span data-stu-id="be7d8-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="be7d8-261">若要使用使用者密碼，請在 `UserSecretsId` 專案檔中定義 `PropertyGroup` 專案。</span><span class="sxs-lookup"><span data-stu-id="be7d8-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="be7d8-262">的內部文字 `UserSecretsId` 是任意的，但對專案而言是唯一的。</span><span class="sxs-lookup"><span data-stu-id="be7d8-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="be7d8-263">開發人員通常會產生的 GUID `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="be7d8-264">在 Visual Studio 中，以滑鼠右鍵按一下方案總管中的專案，然後從內容功能表中選取 [ **管理使用者密碼** ]。</span><span class="sxs-lookup"><span data-stu-id="be7d8-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="be7d8-265">這項手勢會將以 `UserSecretsId` GUID 填入的專案加入至專案檔。</span><span class="sxs-lookup"><span data-stu-id="be7d8-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="be7d8-266">設定秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-266">Set a secret</span></span>

<span data-ttu-id="be7d8-267">定義由機碼及其值組成的應用程式密碼。</span><span class="sxs-lookup"><span data-stu-id="be7d8-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="be7d8-268">此密碼與專案的值相關聯 `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="be7d8-269">例如，從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="be7d8-270">在上述範例中，冒號表示 `Movies` 為具有屬性的物件常值 `ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="be7d8-271">秘密管理員工具也可以從其他目錄使用。</span><span class="sxs-lookup"><span data-stu-id="be7d8-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="be7d8-272">您 `--project` 可以使用選項來提供專案檔所在的檔案系統路徑。</span><span class="sxs-lookup"><span data-stu-id="be7d8-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="be7d8-273">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="be7d8-274">Visual Studio 中的 JSON 結構壓平合併</span><span class="sxs-lookup"><span data-stu-id="be7d8-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="be7d8-275">Visual Studio 的 [ **管理使用者密碼** ] 手勢會在文字編輯器中開啟檔案的 *secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="be7d8-276">以要儲存的索引鍵/值組取代 *secrets.js* 的內容。</span><span class="sxs-lookup"><span data-stu-id="be7d8-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="be7d8-277">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="be7d8-278">JSON 結構會在透過或修改之後進行壓平合併 `dotnet user-secrets remove` `dotnet user-secrets set` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="be7d8-279">例如，執行會折迭 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 物件常值。</span><span class="sxs-lookup"><span data-stu-id="be7d8-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="be7d8-280">修改過的檔案類似下列 JSON：</span><span class="sxs-lookup"><span data-stu-id="be7d8-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="be7d8-281">設定多個秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-281">Set multiple secrets</span></span>

<span data-ttu-id="be7d8-282">您可以使用管線將 JSON 傳送至命令來設定一批秘密 `set` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="be7d8-283">在下列範例中，檔案的內容 *input.js* 會以管道傳送至 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="be7d8-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="be7d8-284">Windows</span><span class="sxs-lookup"><span data-stu-id="be7d8-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="be7d8-285">開啟命令 shell，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="be7d8-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="be7d8-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="be7d8-287">開啟命令 shell，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="be7d8-288">存取秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-288">Access a secret</span></span>

<span data-ttu-id="be7d8-289">設定 [API](xref:fundamentals/configuration/index) 會提供使用者密碼的存取權。</span><span class="sxs-lookup"><span data-stu-id="be7d8-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="be7d8-290">如果您的專案是以 .NET Framework 為目標，請安裝 [Microsoft.Extensions.Configuration。Usersecrets.xml](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="be7d8-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="be7d8-291">在 ASP.NET Core 2.0 或更新版本中，當專案呼叫時，使用者密碼設定來源會自動加入至開發模式 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="be7d8-292">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>當為時 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 呼叫 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="be7d8-293">若 `CreateDefaultBuilder` 未呼叫，請在函式中呼叫，以明確地新增使用者秘密設定來源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="be7d8-294">`AddUserSecrets`只有當應用程式在開發環境中執行時才呼叫，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="be7d8-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="be7d8-295">您可以透過 .NET 設定 API 來抓取使用者密碼：</span><span class="sxs-lookup"><span data-stu-id="be7d8-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="be7d8-296">將秘密對應至 POCO</span><span class="sxs-lookup"><span data-stu-id="be7d8-296">Map secrets to a POCO</span></span>

<span data-ttu-id="be7d8-297">將整個物件常值對應至 POCO (具有屬性) 的簡單 .NET 類別適用于匯總相關的屬性。</span><span class="sxs-lookup"><span data-stu-id="be7d8-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-298">若要將上述秘密對應至 POCO，請使用 .NET 設定 API 的 [物件圖形](xref:fundamentals/configuration/index#bind-to-an-object-graph) 系結功能。</span><span class="sxs-lookup"><span data-stu-id="be7d8-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="be7d8-299">下列程式碼會系結至自訂 `MovieSettings` POCO 並存取 `ServiceApiKey` 屬性值：</span><span class="sxs-lookup"><span data-stu-id="be7d8-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="be7d8-300">`Movies:ConnectionString`和 `Movies:ServiceApiKey` 密碼會對應到中的個別屬性 `MovieSettings` ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="be7d8-301">使用秘密取代字串</span><span class="sxs-lookup"><span data-stu-id="be7d8-301">String replacement with secrets</span></span>

<span data-ttu-id="be7d8-302">以純文字儲存密碼並非安全的。</span><span class="sxs-lookup"><span data-stu-id="be7d8-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="be7d8-303">例如，儲存在中的資料庫連接字串 *appsettings.json* 可能包含指定使用者的密碼：</span><span class="sxs-lookup"><span data-stu-id="be7d8-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="be7d8-304">更安全的方法是將密碼儲存為秘密。</span><span class="sxs-lookup"><span data-stu-id="be7d8-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="be7d8-305">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="be7d8-306">`Password`從的連接字串中移除機碼值組 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="be7d8-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="be7d8-307">例如：</span><span class="sxs-lookup"><span data-stu-id="be7d8-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="be7d8-308">您可以在物件的屬性上設定秘密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成連接字串：</span><span class="sxs-lookup"><span data-stu-id="be7d8-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="be7d8-309">列出秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-310">從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="be7d8-311">下列輸出會出現：</span><span class="sxs-lookup"><span data-stu-id="be7d8-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="be7d8-312">在上述範例中，索引鍵名稱中的冒號代表 *secrets.js* 中的物件階層。</span><span class="sxs-lookup"><span data-stu-id="be7d8-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="be7d8-313">移除單一秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-314">從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="be7d8-315">應用程式 *在檔案上的secrets.js* 已修改為移除與機碼相關聯的索引鍵/值組 `MoviesConnectionString` ：</span><span class="sxs-lookup"><span data-stu-id="be7d8-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="be7d8-316">`dotnet user-secrets list`執行會顯示下列訊息：</span><span class="sxs-lookup"><span data-stu-id="be7d8-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="be7d8-317">移除所有秘密</span><span class="sxs-lookup"><span data-stu-id="be7d8-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="be7d8-318">從專案檔所在的目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="be7d8-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="be7d8-319">應用程式的所有使用者密碼都已從 *secrets.js* 檔案中刪除：</span><span class="sxs-lookup"><span data-stu-id="be7d8-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="be7d8-320">`dotnet user-secrets list`執行會顯示下列訊息：</span><span class="sxs-lookup"><span data-stu-id="be7d8-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="be7d8-321">其他資源</span><span class="sxs-lookup"><span data-stu-id="be7d8-321">Additional resources</span></span>

* <span data-ttu-id="be7d8-322">請參閱 [這個問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) ，以取得從 IIS 存取使用者密碼的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="be7d8-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end