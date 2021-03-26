---
title: ASP.NET Core 中的 Azure Key Vault 設定提供者
author: rick-anderson
description: 瞭解如何使用 Azure Key Vault 設定提供者，使用在執行時間載入的名稱/值配對來設定應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli, contperf-fy21q3
ms.date: 03/17/2021
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
uid: security/key-vault-configuration
ms.openlocfilehash: 940458147f1a03eb2964518dc0e1b042775d693e
ms.sourcegitcommit: 9d185d06b343ec2fb91e7c01a4b14dd924f041c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2021
ms.locfileid: "105614909"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="3760c-103">ASP.NET Core 中的 Azure Key Vault 設定提供者</span><span class="sxs-lookup"><span data-stu-id="3760c-103">Azure Key Vault configuration provider in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3760c-104">本檔說明如何使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 設定提供者，從 Azure Key Vault 秘密載入應用程式設定值。</span><span class="sxs-lookup"><span data-stu-id="3760c-104">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) configuration provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="3760c-105">Azure Key Vault 是以雲端為基礎的服務，可協助保護應用程式和服務所使用的密碼編譯金鑰和秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-105">Azure Key Vault is a cloud-based service that helps safeguard cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="3760c-106">搭配 ASP.NET Core 應用程式使用 Azure Key Vault 的常見案例包括：</span><span class="sxs-lookup"><span data-stu-id="3760c-106">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="3760c-107">控制對敏感設定資料的存取。</span><span class="sxs-lookup"><span data-stu-id="3760c-107">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="3760c-108">在儲存設定資料時，符合 FIPS 140-2 Level 2 驗證的硬體安全模組 (Hsm) 的需求。</span><span class="sxs-lookup"><span data-stu-id="3760c-108">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSMs) when storing configuration data.</span></span>

<span data-ttu-id="3760c-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3760c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="3760c-110">套件</span><span class="sxs-lookup"><span data-stu-id="3760c-110">Packages</span></span>

<span data-ttu-id="3760c-111">新增下列封裝的套件參考：</span><span class="sxs-lookup"><span data-stu-id="3760c-111">Add package references for the following packages:</span></span>

* [<span data-ttu-id="3760c-112">Azure.Extensions.AspNetCore.Configuration。秘密</span><span class="sxs-lookup"><span data-stu-id="3760c-112">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="3760c-113">[蔚藍。Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="3760c-113">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="3760c-114">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="3760c-114">Sample app</span></span>

<span data-ttu-id="3760c-115">範例應用程式會以 `#define` 程式最上層的預處理器指示詞所決定的兩種模式之一執行 *。*</span><span class="sxs-lookup"><span data-stu-id="3760c-115">The sample app runs in either of two modes determined by the `#define` preprocessor directive at the top of *Program.cs*:</span></span>

* <span data-ttu-id="3760c-116">`Certificate`：示範如何使用 Azure Key Vault 用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-116">`Certificate`: Demonstrates using an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="3760c-117">您可以從任何位置執行此範例，無論部署到 Azure App Service 或任何可提供 ASP.NET Core 應用程式的主機。</span><span class="sxs-lookup"><span data-stu-id="3760c-117">This sample can be run from any location, whether deployed to Azure App Service or any host that can serve an ASP.NET Core app.</span></span>
* <span data-ttu-id="3760c-118">`Managed`：示範如何使用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別。</span><span class="sxs-lookup"><span data-stu-id="3760c-118">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview).</span></span> <span data-ttu-id="3760c-119">受控識別會驗證應用程式，以 Azure Active Directory (AD) authentication 來 Azure Key Vault，而不需要將認證儲存在應用程式的程式碼或設定中。</span><span class="sxs-lookup"><span data-stu-id="3760c-119">The managed identity authenticates the app to Azure Key Vault with Azure Active Directory (AD) authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="3760c-120">使用受控識別進行驗證時，) 不需要 Azure AD 的應用程式識別碼和密碼 (用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="3760c-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="3760c-121">`Managed`範例的版本必須部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="3760c-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="3760c-122">Follow the guidance in the [Use the managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span><span class="sxs-lookup"><span data-stu-id="3760c-122">Follow the guidance in the [Use the managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="3760c-123">如需使用預處理器指示詞 () 設定範例應用程式的詳細資訊 `#define` ，請參閱 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="3760c-123">For more information configuring a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="3760c-124">開發環境中的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="3760c-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="3760c-125">使用 [秘密管理員](xref:security/app-secrets)在本機設定秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-125">Set secrets locally using [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="3760c-126">當範例應用程式在開發環境中的本機電腦上執行時，會從本機使用者秘密存放區載入秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="3760c-127">秘密管理員需要 `<UserSecretsId>` 應用程式專案檔案中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3760c-127">Secret Manager requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="3760c-128">將屬性值 (`{GUID}`) 設定為任何唯一的 GUID：</span><span class="sxs-lookup"><span data-stu-id="3760c-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="3760c-129">秘密會以名稱/值組的形式建立。</span><span class="sxs-lookup"><span data-stu-id="3760c-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="3760c-130">階層值 (設定區段) 使用 `:` (冒號) 做為 [ASP.NET Core](xref:fundamentals/configuration/index) 設定索引鍵名稱中的分隔符號。</span><span class="sxs-lookup"><span data-stu-id="3760c-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="3760c-131">秘密管理員是從開啟至專案 [內容根目錄](xref:fundamentals/index#content-root)的命令 shell 使用，其中 `{SECRET NAME}` 是名稱，而 `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="3760c-131">Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="3760c-132">從專案的 [內容根目錄](xref:fundamentals/index#content-root) 在命令 shell 中執行下列命令，以設定範例應用程式的密碼：</span><span class="sxs-lookup"><span data-stu-id="3760c-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="3760c-133">當這些秘密儲存在 [具有 Azure Key Vault 區段的實際執行環境 Azure Key Vault 的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中時， `_dev` 尾碼會變更為 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="3760c-134">尾碼會在應用程式的輸出中提供視覺提示，指出設定值的來源。</span><span class="sxs-lookup"><span data-stu-id="3760c-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="3760c-135">在生產環境中使用 Azure Key Vault 的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="3760c-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="3760c-136">請完成下列步驟來建立 Azure Key Vault，並在其中儲存範例應用程式的秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-136">Complete the following steps to create an Azure Key Vault and store the sample app's secrets in it.</span></span> <span data-ttu-id="3760c-137">如需詳細資訊，請參閱[快速入門：使用 Azure CLI 從 Azure Key Vault 設定及擷取祕密](/azure/key-vault/quick-create-cli)。</span><span class="sxs-lookup"><span data-stu-id="3760c-137">For more information, see [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli).</span></span>

1. <span data-ttu-id="3760c-138">使用 [Azure 入口網站](https://portal.azure.com/)中的下列其中一種方法開啟 Azure Cloud Shell：</span><span class="sxs-lookup"><span data-stu-id="3760c-138">Open Azure Cloud Shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="3760c-139">選取程式碼區塊右上角的 [試試看]。</span><span class="sxs-lookup"><span data-stu-id="3760c-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="3760c-140">在文字方塊中使用搜尋字串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="3760c-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="3760c-141">在您的瀏覽器中，使用 [ **啟動 Cloud Shell** ] 按鈕開啟 Cloud Shell。</span><span class="sxs-lookup"><span data-stu-id="3760c-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="3760c-142">選取 Azure 入口網站右上角功能表上的 **[Cloud Shell]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3760c-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="3760c-143">如需詳細資訊，請參閱[Azure Cloud Shell 的](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)和總覽。</span><span class="sxs-lookup"><span data-stu-id="3760c-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="3760c-144">如果您尚未經過驗證，請使用命令登入 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="3760c-145">使用下列命令建立資源群組，其中 `{RESOURCE GROUP NAME}` 是新的資源群組的名稱，而 `{LOCATION}` 是 Azure 區域：</span><span class="sxs-lookup"><span data-stu-id="3760c-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the new resource group's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3760c-146">使用下列命令在資源群組中建立金鑰保存庫，其中 `{KEY VAULT NAME}` 是新的金鑰保存庫名稱，而且 `{LOCATION}` 是 Azure 區域：</span><span class="sxs-lookup"><span data-stu-id="3760c-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the new key vault's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3760c-147">以名稱/值組的形式在金鑰保存庫中建立秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="3760c-148">Azure Key Vault 秘密名稱僅限英數位元和連字號。</span><span class="sxs-lookup"><span data-stu-id="3760c-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="3760c-149">階層值 (設定區段) 使用 `--` (兩個虛線) 作為分隔符號，因為金鑰保存庫秘密名稱中不允許有冒號。</span><span class="sxs-lookup"><span data-stu-id="3760c-149">Hierarchical values (configuration sections) use `--` (two dashes) as a delimiter, as colons aren't allowed in key vault secret names.</span></span> <span data-ttu-id="3760c-150">冒號會從 [ASP.NET Core](xref:fundamentals/configuration/index)設定中的子機碼分隔區段。</span><span class="sxs-lookup"><span data-stu-id="3760c-150">Colons delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3760c-151">將秘密載入至應用程式的設定時，會以冒號取代雙虛線序列。</span><span class="sxs-lookup"><span data-stu-id="3760c-151">The two-dash sequence is replaced with a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="3760c-152">下列秘密適用于範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="3760c-153">這些值包含後置詞 `_prod` ，以便與 `_dev` 秘密管理員從開發環境中載入的尾碼值區別。</span><span class="sxs-lookup"><span data-stu-id="3760c-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from Secret Manager.</span></span> <span data-ttu-id="3760c-154">將取代 `{KEY VAULT NAME}` 為您在先前步驟中建立的金鑰保存庫名稱：</span><span class="sxs-lookup"><span data-stu-id="3760c-154">Replace `{KEY VAULT NAME}` with the name of the key vault you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="3760c-155">針對非 Azure 託管應用程式使用應用程式識別碼和 x.509 憑證</span><span class="sxs-lookup"><span data-stu-id="3760c-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="3760c-156">設定 Azure AD、Azure Key Vault 和應用程式，以在 **應用程式裝載于 Azure 外部時**，使用 Azure AD 應用程式識別碼和 x.509 憑證來向金鑰保存庫進行驗證。</span><span class="sxs-lookup"><span data-stu-id="3760c-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure AD Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="3760c-157">如需詳細資訊，請參閱[關於金鑰、祕密和憑證](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="3760c-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="3760c-158">雖然 Azure 中託管的應用程式支援使用應用程式識別碼和 x.509 憑證，但不建議使用。</span><span class="sxs-lookup"><span data-stu-id="3760c-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, it's not recommended.</span></span> <span data-ttu-id="3760c-159">請改為在 Azure 中裝載應用程式時，使用 [適用于 azure 資源的受控](#use-managed-identities-for-azure-resources) 識別。</span><span class="sxs-lookup"><span data-stu-id="3760c-159">Instead, use [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="3760c-160">受控識別不需要將憑證儲存在應用程式或開發環境中。</span><span class="sxs-lookup"><span data-stu-id="3760c-160">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="3760c-161">當 `#define` *Program* top 的預處理器指示詞設定為時，範例應用程式會使用應用程式識別碼和 x.509 憑證 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-161">The sample app uses an Application ID and X.509 certificate when the `#define` preprocessor directive at the top of *Program.cs* is set to `Certificate`.</span></span>

1. <span data-ttu-id="3760c-162">建立 PKCS # 12 封存 (*.pfx*) 憑證。</span><span class="sxs-lookup"><span data-stu-id="3760c-162">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="3760c-163">建立憑證的選項包括 [Windows](/windows/desktop/seccrypto/makecert) 和 [OpenSSL](https://www.openssl.org/)上的 MakeCert。</span><span class="sxs-lookup"><span data-stu-id="3760c-163">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="3760c-164">將憑證安裝到目前使用者的個人憑證存儲中。</span><span class="sxs-lookup"><span data-stu-id="3760c-164">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="3760c-165">將金鑰標示為可匯出是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="3760c-165">Marking the key as exportable is optional.</span></span> <span data-ttu-id="3760c-166">請注意憑證的指紋，此憑證稍後會在此程式中使用。</span><span class="sxs-lookup"><span data-stu-id="3760c-166">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="3760c-167">將 PKCS # 12 封存 (*.pfx*) 憑證匯出為 DER 編碼憑證 (*.cer*) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-167">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="3760c-168">使用 Azure AD (**應用程式註冊**) 註冊應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-168">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="3760c-169">將 DER 編碼憑證 (*.cer*) 上傳至 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="3760c-169">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="3760c-170">在 Azure AD 中選取應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-170">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="3760c-171">流覽至 **憑證 & 秘密**。</span><span class="sxs-lookup"><span data-stu-id="3760c-171">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="3760c-172">選取 [ **上傳憑證** ] 以上傳包含公開金鑰的憑證。</span><span class="sxs-lookup"><span data-stu-id="3760c-172">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="3760c-173">*.Cer*、 *pem* 或 *.crt* 憑證是可接受的。</span><span class="sxs-lookup"><span data-stu-id="3760c-173">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="3760c-174">將金鑰保存庫名稱、應用程式識別碼和憑證指紋儲存在應用程式的檔案中 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3760c-174">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="3760c-175">流覽至 Azure 入口網站中的 **金鑰保存庫** 。</span><span class="sxs-lookup"><span data-stu-id="3760c-175">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="3760c-176">使用 Azure Key Vault 區段，選取您在 [生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中建立的金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-176">Select the key vault you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="3760c-177">選取 [存取原則]。</span><span class="sxs-lookup"><span data-stu-id="3760c-177">Select **Access policies**.</span></span>
1. <span data-ttu-id="3760c-178">選取 [新增存取原則]。</span><span class="sxs-lookup"><span data-stu-id="3760c-178">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="3760c-179">開啟 **秘密許可權** ，並提供具有 **取得** 和 **列出** 許可權的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-179">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="3760c-180">選取 [ **選取主體** ]，然後依名稱選取已註冊的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-180">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="3760c-181">選取 [選取]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="3760c-181">Select the **Select** button.</span></span>
1. <span data-ttu-id="3760c-182">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="3760c-182">Select **OK**.</span></span>
1. <span data-ttu-id="3760c-183">選取 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="3760c-183">Select **Save**.</span></span>
1. <span data-ttu-id="3760c-184">部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-184">Deploy the app.</span></span>

<span data-ttu-id="3760c-185">`Certificate`範例應用程式會從 `IConfigurationRoot` 與秘密名稱相同的名稱取得其設定值：</span><span class="sxs-lookup"><span data-stu-id="3760c-185">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="3760c-186">非階層式值：的值 `SecretName` 是使用取得 `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-186">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="3760c-187">階層值 (區段) ：使用 `:` (冒號) 標記法或 `GetSection` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="3760c-187">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="3760c-188">您可以使用下列其中一種方法來取得設定值：</span><span class="sxs-lookup"><span data-stu-id="3760c-188">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="3760c-189">X.509 憑證由作業系統管理。</span><span class="sxs-lookup"><span data-stu-id="3760c-189">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="3760c-190">應用程式會 `AddAzureKeyVault` 使用檔案所提供的值來呼叫 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="3760c-190">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="3760c-191">範例值：</span><span class="sxs-lookup"><span data-stu-id="3760c-191">Example values:</span></span>

* <span data-ttu-id="3760c-192">金鑰保存庫名稱： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="3760c-192">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="3760c-193">應用程式識別碼： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="3760c-193">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="3760c-194">憑證指紋： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="3760c-194">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="3760c-195">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3760c-195">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="3760c-196">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-196">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3760c-197">在開發環境中，秘密值會以 `_dev` 尾碼載入。</span><span class="sxs-lookup"><span data-stu-id="3760c-197">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="3760c-198">在生產環境中，值會以後綴載入 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-198">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="3760c-199">使用適用於 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="3760c-199">Use managed identities for Azure resources</span></span>

<span data-ttu-id="3760c-200">**部署至 azure 的應用程式** 可以利用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別。</span><span class="sxs-lookup"><span data-stu-id="3760c-200">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview).</span></span> <span data-ttu-id="3760c-201">受控識別可讓應用程式使用 Azure AD 驗證來驗證 Azure Key Vault，而不需要認證 (應用程式識別碼和密碼/用戶端秘密) 儲存在應用程式中。</span><span class="sxs-lookup"><span data-stu-id="3760c-201">A managed identity allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="3760c-202">當 `#define` *Program* 頂端的預處理器指示詞設定為時，範例應用程式會使用適用于 Azure 資源的受控識別 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-202">The sample app uses managed identities for Azure resources when the `#define` preprocessor directive at the top of *Program.cs* is set to `Managed`.</span></span>

<span data-ttu-id="3760c-203">將保存庫名稱輸入應用程式的檔案 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3760c-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="3760c-204">範例應用程式在設定為版本時，不需要 (用戶端密碼) 的應用程式識別碼和密碼 `Managed` ，因此您可以忽略這些設定專案。</span><span class="sxs-lookup"><span data-stu-id="3760c-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="3760c-205">應用程式會部署至 Azure，而 Azure 會使用儲存在檔案中的保存庫名稱來驗證應用程式，以存取 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3760c-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="3760c-206">將範例應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="3760c-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="3760c-207">部署到 Azure App Service 的應用程式會在建立服務時自動向 Azure AD 註冊。</span><span class="sxs-lookup"><span data-stu-id="3760c-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="3760c-208">從部署中取得物件識別碼，以在下列命令中使用。</span><span class="sxs-lookup"><span data-stu-id="3760c-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="3760c-209">物件識別碼會顯示在 App Service 面板的 [Azure 入口網站] 中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="3760c-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="3760c-210">使用 Azure CLI 和應用程式的物件識別碼，為應用程式提供 `list` `get` 存取金鑰保存庫的和許可權：</span><span class="sxs-lookup"><span data-stu-id="3760c-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="3760c-211">使用 Azure CLI、PowerShell 或 Azure 入口網站 **重新開機應用程式**。</span><span class="sxs-lookup"><span data-stu-id="3760c-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="3760c-212">範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="3760c-212">The sample app:</span></span>

* <span data-ttu-id="3760c-213">建立 <xref:Azure.Identity.DefaultAzureCredential> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="3760c-213">Creates an instance of the <xref:Azure.Identity.DefaultAzureCredential> class.</span></span> <span data-ttu-id="3760c-214">認證會嘗試從 Azure 資源的環境取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="3760c-214">The credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="3760c-215"><xref:Azure.Security.KeyVault.Secrets.SecretClient>使用實例建立新的 `DefaultAzureCredential` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-215">A new <xref:Azure.Security.KeyVault.Secrets.SecretClient> is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="3760c-216">此 `SecretClient` 實例會與實例搭配使用 `KeyVaultSecretManager` ，它會載入秘密值並以冒號取代雙虛線 (`--`)  (`:`) 的索引鍵名稱。</span><span class="sxs-lookup"><span data-stu-id="3760c-216">The `SecretClient` instance is used with a `KeyVaultSecretManager` instance, which loads secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="3760c-217">金鑰保存庫名稱範例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="3760c-217">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="3760c-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3760c-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="3760c-219">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3760c-220">在開發環境中，秘密值具有 `_dev` 尾碼，因為它們是由秘密管理員所提供。</span><span class="sxs-lookup"><span data-stu-id="3760c-220">In the Development environment, secret values have the `_dev` suffix because they're provided by Secret Manager.</span></span> <span data-ttu-id="3760c-221">在生產環境中，值會以後綴載入， `_prod` 因為它們是由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="3760c-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="3760c-222">如果您收到 `Access denied` 錯誤，請確認已向 Azure AD 註冊應用程式，並提供金鑰保存庫的存取權。</span><span class="sxs-lookup"><span data-stu-id="3760c-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="3760c-223">確認您已在 Azure 中重新開機服務。</span><span class="sxs-lookup"><span data-stu-id="3760c-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="3760c-224">如需搭配受控識別和 Azure Pipelines 使用提供者的詳細資訊，請參閱使用 [受控服務識別建立與 VM 的 Azure Resource Manager 服務](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)連線。</span><span class="sxs-lookup"><span data-stu-id="3760c-224">For information on using the provider with a managed identity and Azure Pipelines, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="3760c-225">設定選項</span><span class="sxs-lookup"><span data-stu-id="3760c-225">Configuration options</span></span>

<span data-ttu-id="3760c-226">`AddAzureKeyVault` 可以接受 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> 物件：</span><span class="sxs-lookup"><span data-stu-id="3760c-226">`AddAzureKeyVault` can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> object:</span></span>

```csharp
config.AddAzureKeyVault(
    new SecretClient(
        new Uri("Your Key Vault Endpoint"),
        new DefaultAzureCredential()),
        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

<span data-ttu-id="3760c-227">此 `AzureKeyVaultConfigurationOptions` 物件包含下列屬性。</span><span class="sxs-lookup"><span data-stu-id="3760c-227">The `AzureKeyVaultConfigurationOptions` object contains the following properties.</span></span>

| <span data-ttu-id="3760c-228">屬性</span><span class="sxs-lookup"><span data-stu-id="3760c-228">Property</span></span>         | <span data-ttu-id="3760c-229">描述</span><span class="sxs-lookup"><span data-stu-id="3760c-229">Description</span></span> |
| ---------------- | ----------- |
| <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions.Manager%2A>        | <span data-ttu-id="3760c-230"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 用來控制秘密載入的實例。</span><span class="sxs-lookup"><span data-stu-id="3760c-230"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions.ReloadInterval%2A> | <span data-ttu-id="3760c-231">`TimeSpan` 在輪詢金鑰保存庫以進行變更的嘗試之間等候。</span><span class="sxs-lookup"><span data-stu-id="3760c-231">`TimeSpan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="3760c-232">預設值為 `null` (設定) 不會重載。</span><span class="sxs-lookup"><span data-stu-id="3760c-232">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="3760c-233">使用金鑰名稱前置詞</span><span class="sxs-lookup"><span data-stu-id="3760c-233">Use a key name prefix</span></span>

<span data-ttu-id="3760c-234">`AddAzureKeyVault` 提供接受執行的多載，可 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 讓您控制如何將金鑰保存庫密碼轉換成設定金鑰。</span><span class="sxs-lookup"><span data-stu-id="3760c-234">`AddAzureKeyVault` provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="3760c-235">例如，您可以根據您在應用程式啟動時所提供的前置詞值，執行介面以載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-235">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="3760c-236">例如，這項技術可讓您根據應用程式的版本載入秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-236">This technique allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="3760c-237">請勿在 key vault 秘密上使用前置詞來：</span><span class="sxs-lookup"><span data-stu-id="3760c-237">Don't use prefixes on key vault secrets to:</span></span>
>
> * <span data-ttu-id="3760c-238">將多個應用程式的秘密放入相同的保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-238">Place secrets for multiple apps into the same vault.</span></span>
> * <span data-ttu-id="3760c-239">將環境秘密 (例如， *開發* 和 *生產* 秘密) 放入相同的保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-239">Place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span>
> 
> <span data-ttu-id="3760c-240">不同的應用程式和開發/生產環境應該使用不同的金鑰保存庫來隔離應用程式環境，以獲得最高層級的安全性。</span><span class="sxs-lookup"><span data-stu-id="3760c-240">Different apps and development/production environments should use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="3760c-241">下列範例會在金鑰保存庫中建立秘密 (並且針對開發環境使用秘密管理員) `5000-AppSecret` (期間不允許金鑰保存庫秘密名稱) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-241">In the following example, a secret is established in the key vault (and using Secret Manager for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="3760c-242">此秘密代表應用程式版本5.0.0.0 的應用程式密碼。</span><span class="sxs-lookup"><span data-stu-id="3760c-242">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="3760c-243">針對另一個版本的應用程式，5.1.0.0 會將秘密新增至金鑰保存庫 (並使用的秘密管理員) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-243">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using Secret Manager) for `5100-AppSecret`.</span></span> <span data-ttu-id="3760c-244">每個應用程式版本都會將其已建立版本的秘密值載入至其設定 `AppSecret` 中，以在載入密碼時移除版本。</span><span class="sxs-lookup"><span data-stu-id="3760c-244">Each app version loads its versioned secret value into its configuration as `AppSecret`, removing the version as it loads the secret.</span></span>

<span data-ttu-id="3760c-245">`AddAzureKeyVault` 使用自訂實作為呼叫 `IKeyVaultSecretManager` ：</span><span class="sxs-lookup"><span data-stu-id="3760c-245">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager` implementation:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="3760c-246">此執行會回應秘密的版本前置詞，以將適當的秘密載入設定：</span><span class="sxs-lookup"><span data-stu-id="3760c-246">The implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="3760c-247">`Load` 在名稱開頭為前置詞時載入秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-247">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="3760c-248">未載入其他秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-248">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="3760c-249">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="3760c-249">`GetKey`:</span></span>
  * <span data-ttu-id="3760c-250">移除秘密名稱中的前置詞。</span><span class="sxs-lookup"><span data-stu-id="3760c-250">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="3760c-251">使用的任何名稱取代兩個虛線 `KeyDelimiter` ，也就是設定 (中使用的分隔符號，通常是) 的冒號。</span><span class="sxs-lookup"><span data-stu-id="3760c-251">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="3760c-252">Azure Key Vault 不允許秘密名稱中有冒號。</span><span class="sxs-lookup"><span data-stu-id="3760c-252">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/PrefixKeyVaultSecretManager.cs)]

<span data-ttu-id="3760c-253">`Load`方法是由可逐一查看保存庫密碼的提供者演算法呼叫，以找出版本首碼的秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-253">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the version-prefixed secrets.</span></span> <span data-ttu-id="3760c-254">當找到版本首碼時 `Load` ，演算法會使用 `GetKey` 方法來傳回秘密名稱的設定名稱。</span><span class="sxs-lookup"><span data-stu-id="3760c-254">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="3760c-255">它會移除秘密名稱的版本前置詞。</span><span class="sxs-lookup"><span data-stu-id="3760c-255">It removes the version prefix from the secret's name.</span></span> <span data-ttu-id="3760c-256">會傳回秘密名稱的其餘部分，以供載入至應用程式的設定名稱-值配對。</span><span class="sxs-lookup"><span data-stu-id="3760c-256">The rest of the secret name is returned for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="3760c-257">當此方法實施時：</span><span class="sxs-lookup"><span data-stu-id="3760c-257">When this approach is implemented:</span></span>

1. <span data-ttu-id="3760c-258">應用程式的專案檔中所指定的應用程式版本。</span><span class="sxs-lookup"><span data-stu-id="3760c-258">The app's version specified in the app's project file.</span></span> <span data-ttu-id="3760c-259">在下列範例中，應用程式的版本會設定為 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="3760c-259">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="3760c-260">確認 `<UserSecretsId>` 屬性存在於應用程式的專案檔中，其中 `{GUID}` 是使用者提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="3760c-260">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="3760c-261">使用 [秘密管理員](xref:security/app-secrets)在本機儲存下列秘密：</span><span class="sxs-lookup"><span data-stu-id="3760c-261">Save the following secrets locally with [Secret Manager](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="3760c-262">使用下列 Azure CLI 命令，將秘密儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="3760c-262">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="3760c-263">執行應用程式時，會載入金鑰保存庫秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-263">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="3760c-264">的字串秘密 `5000-AppSecret` 會與應用程式專案檔中所指定的應用程式版本相符 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-264">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="3760c-265">`5000`以虛線)  (的版本會從索引鍵名稱中移除。</span><span class="sxs-lookup"><span data-stu-id="3760c-265">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="3760c-266">在整個應用程式中，使用金鑰讀取設定會 `AppSecret` 載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-266">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="3760c-267">如果將專案檔中的應用程式版本變更為 `5.1.0.0` ，並再次執行應用程式，則傳回的秘密值會 `5.1.0.0_secret_value_dev` 在開發環境和生產環境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-267">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="3760c-268">您也可以提供自己的 <xref:Azure.Security.KeyVault.Secrets.SecretClient> 實作為 `AddAzureKeyVault` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-268">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="3760c-269">自訂用戶端允許跨應用程式共用用戶端的單一實例。</span><span class="sxs-lookup"><span data-stu-id="3760c-269">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="3760c-270">將陣列繫結到類別</span><span class="sxs-lookup"><span data-stu-id="3760c-270">Bind an array to a class</span></span>

<span data-ttu-id="3760c-271">提供者可以將設定值讀取至陣列，以系結至 POCO 陣列。</span><span class="sxs-lookup"><span data-stu-id="3760c-271">The provider can read configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="3760c-272">從允許金鑰包含冒號 () 分隔符號的設定來源進行讀取時 `:` ，會使用數值索引鍵區段來區分組成陣列的索引鍵 (`:0:` 、 `:1:` 、 &hellip; `:{n}:`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-272">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="3760c-273">如需詳細資訊，請參閱設定：將陣列系結 [至類別](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="3760c-273">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="3760c-274">Azure Key Vault 金鑰不能使用冒號做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="3760c-274">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="3760c-275">本檔中所述的方法會使用雙虛線 (`--`) 做為階層值的分隔符號 (區段) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-275">The approach described in this document uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="3760c-276">陣列索引鍵會儲存在 Azure Key Vault 中，並以雙連字號和數位鍵區段 (`--0--` 、 `--1--` &hellip; `--{n}--`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-276">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="3760c-277">檢查 JSON 檔案所提供的下列 [Serilog](https://serilog.net/) 記錄提供者設定。</span><span class="sxs-lookup"><span data-stu-id="3760c-277">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="3760c-278">陣列中定義了兩個物件常值 `WriteTo` ，它會反映兩個 Serilog *接收*，其描述記錄輸出的目的地：</span><span class="sxs-lookup"><span data-stu-id="3760c-278">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="3760c-279">先前 JSON 檔案中顯示的設定會使用雙虛線 (`--`) 標記法和數位區段，儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="3760c-279">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="3760c-280">Key</span><span class="sxs-lookup"><span data-stu-id="3760c-280">Key</span></span> | <span data-ttu-id="3760c-281">值</span><span class="sxs-lookup"><span data-stu-id="3760c-281">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="3760c-282">重載秘密</span><span class="sxs-lookup"><span data-stu-id="3760c-282">Reload secrets</span></span>

<span data-ttu-id="3760c-283">在呼叫之前，會快取秘密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-283">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="3760c-284">在執行之前，應用程式不會遵守金鑰保存庫中已過期、已停用和更新的秘密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-284">Expired, disabled, and updated secrets in the key vault aren't respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="3760c-285">已停用和過期的秘密</span><span class="sxs-lookup"><span data-stu-id="3760c-285">Disabled and expired secrets</span></span>

<span data-ttu-id="3760c-286">停用和過期的秘密會擲回 <xref:Azure.RequestFailedException> 。</span><span class="sxs-lookup"><span data-stu-id="3760c-286">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="3760c-287">若要防止應用程式擲回，請使用不同的設定提供者來提供設定，或更新已停用或過期的密碼。</span><span class="sxs-lookup"><span data-stu-id="3760c-287">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3760c-288">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3760c-288">Troubleshoot</span></span>

<span data-ttu-id="3760c-289">當應用程式無法使用提供者載入設定時，會將錯誤訊息寫入 [ASP.NET Core 記錄基礎結構](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="3760c-289">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3760c-290">下列情況會導致無法載入設定：</span><span class="sxs-lookup"><span data-stu-id="3760c-290">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="3760c-291">Azure AD 中的應用程式或憑證未正確設定。</span><span class="sxs-lookup"><span data-stu-id="3760c-291">The app or certificate isn't configured correctly in Azure AD.</span></span>
* <span data-ttu-id="3760c-292">金鑰保存庫不存在於 Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="3760c-292">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="3760c-293">應用程式未獲授權存取金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-293">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="3760c-294">存取原則不包含 `Get` 和 `List` 許可權。</span><span class="sxs-lookup"><span data-stu-id="3760c-294">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="3760c-295">在金鑰保存庫中，設定資料 (的名稱/值組) 不正確地命名、遺失、停用或過期。</span><span class="sxs-lookup"><span data-stu-id="3760c-295">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="3760c-296">應用程式有錯誤的金鑰保存庫名稱 (`KeyVaultName`) 、Azure AD 應用程式識別碼 (`AzureADApplicationId`) 或 Azure AD 憑證指紋 () `AzureADCertThumbprint` ，或 Azure AD 目錄識別碼 () `AzureADDirectoryId` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-296">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application ID (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD Directory ID (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="3760c-297">在您嘗試載入的值的應用程式中，設定機碼 (名稱) 不正確。</span><span class="sxs-lookup"><span data-stu-id="3760c-297">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="3760c-298">新增應用程式的金鑰保存庫存取原則時，已建立原則，但未在 [**存取原則**] UI 中選取 [**儲存**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3760c-298">When adding the key vault access policy for the app, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3760c-299">其他資源</span><span class="sxs-lookup"><span data-stu-id="3760c-299">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="3760c-300">Microsoft Azure： Key Vault 檔</span><span class="sxs-lookup"><span data-stu-id="3760c-300">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="3760c-301">如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰</span><span class="sxs-lookup"><span data-stu-id="3760c-301">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="3760c-302">快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密</span><span class="sxs-lookup"><span data-stu-id="3760c-302">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="3760c-303">教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Windows 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="3760c-303">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
* [<span data-ttu-id="3760c-304">使用 .NET 和 Azure Key Vault Secretless 應用程式</span><span class="sxs-lookup"><span data-stu-id="3760c-304">Secretless apps with .NET and Azure Key Vault</span></span>](https://channel9.msdn.com/Shows/On-NET/Secretless-apps-with-NET-and-Azure-Key-Vault)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3760c-305">本檔說明如何使用 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 設定提供者，從 Azure Key Vault 秘密載入應用程式設定值。</span><span class="sxs-lookup"><span data-stu-id="3760c-305">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) configuration provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="3760c-306">Azure Key Vault 是以雲端為基礎的服務，可協助保護應用程式和服務所使用的密碼編譯金鑰和秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-306">Azure Key Vault is a cloud-based service that helps safeguard cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="3760c-307">搭配 ASP.NET Core 應用程式使用 Azure Key Vault 的常見案例包括：</span><span class="sxs-lookup"><span data-stu-id="3760c-307">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="3760c-308">控制對敏感設定資料的存取。</span><span class="sxs-lookup"><span data-stu-id="3760c-308">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="3760c-309">在儲存設定資料時，符合 FIPS 140-2 Level 2 驗證的硬體安全模組 (Hsm) 的需求。</span><span class="sxs-lookup"><span data-stu-id="3760c-309">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSMs) when storing configuration data.</span></span>

<span data-ttu-id="3760c-310">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3760c-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="3760c-311">套件</span><span class="sxs-lookup"><span data-stu-id="3760c-311">Packages</span></span>

<span data-ttu-id="3760c-312">將封裝參考新增至 [Microsoft.Extensions.Configuration。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 封裝。</span><span class="sxs-lookup"><span data-stu-id="3760c-312">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="3760c-313">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="3760c-313">Sample app</span></span>

<span data-ttu-id="3760c-314">範例應用程式會以 `#define` 程式最上層的預處理器指示詞所決定的兩種模式之一執行 *。*</span><span class="sxs-lookup"><span data-stu-id="3760c-314">The sample app runs in either of two modes determined by the `#define` preprocessor directive at the top of *Program.cs*:</span></span>

* <span data-ttu-id="3760c-315">`Certificate`：示範如何使用 Azure Key Vault 用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-315">`Certificate`: Demonstrates using an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="3760c-316">您可以從任何位置執行此範例，無論部署到 Azure App Service 或任何可提供 ASP.NET Core 應用程式的主機。</span><span class="sxs-lookup"><span data-stu-id="3760c-316">This sample can be run from any location, whether deployed to Azure App Service or any host that can serve an ASP.NET Core app.</span></span>
* <span data-ttu-id="3760c-317">`Managed`：示範如何使用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview) 識別來驗證應用程式，以使用 AZURE ACTIVE DIRECTORY (AD) authentication 來 Azure Key Vault，而不需要將認證儲存在應用程式的程式碼或設定中。</span><span class="sxs-lookup"><span data-stu-id="3760c-317">`Managed`: Demonstrates using [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure Active Directory (AD) authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="3760c-318">使用受控識別進行驗證時，) 不需要 Azure AD 的應用程式識別碼和密碼 (用戶端密碼。</span><span class="sxs-lookup"><span data-stu-id="3760c-318">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="3760c-319">`Managed`範例的版本必須部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="3760c-319">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="3760c-320">Follow the guidance in the [Use the managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span><span class="sxs-lookup"><span data-stu-id="3760c-320">Follow the guidance in the [Use the managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="3760c-321">如需使用預處理器指示詞 () 設定範例應用程式的詳細資訊 `#define` ，請參閱 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="3760c-321">For more information configuring a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="3760c-322">開發環境中的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="3760c-322">Secret storage in the Development environment</span></span>

<span data-ttu-id="3760c-323">使用 [秘密管理員](xref:security/app-secrets)在本機設定秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-323">Set secrets locally using [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="3760c-324">當範例應用程式在開發環境中的本機電腦上執行時，會從本機使用者秘密存放區載入秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-324">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="3760c-325">秘密管理員需要 `<UserSecretsId>` 應用程式專案檔案中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3760c-325">Secret Manager requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="3760c-326">將屬性值 (`{GUID}`) 設定為任何唯一的 GUID：</span><span class="sxs-lookup"><span data-stu-id="3760c-326">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="3760c-327">秘密會以名稱/值組的形式建立。</span><span class="sxs-lookup"><span data-stu-id="3760c-327">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="3760c-328">階層值 (設定區段) 使用 `:` (冒號) 做為 [ASP.NET Core](xref:fundamentals/configuration/index) 設定索引鍵名稱中的分隔符號。</span><span class="sxs-lookup"><span data-stu-id="3760c-328">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="3760c-329">秘密管理員是從開啟至專案 [內容根目錄](xref:fundamentals/index#content-root)的命令 shell 使用，其中 `{SECRET NAME}` 是名稱，而 `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="3760c-329">Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="3760c-330">從專案的 [內容根目錄](xref:fundamentals/index#content-root) 在命令 shell 中執行下列命令，以設定範例應用程式的密碼：</span><span class="sxs-lookup"><span data-stu-id="3760c-330">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="3760c-331">當這些秘密儲存在 [具有 Azure Key Vault 區段的實際執行環境 Azure Key Vault 的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中時， `_dev` 尾碼會變更為 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-331">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="3760c-332">尾碼會在應用程式的輸出中提供視覺提示，指出設定值的來源。</span><span class="sxs-lookup"><span data-stu-id="3760c-332">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="3760c-333">在生產環境中使用 Azure Key Vault 的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="3760c-333">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="3760c-334">[快速入門：使用 Azure CLI 從 Azure Key Vault 設定及取出秘密](/azure/key-vault/quick-create-cli)的摘要摘要如下，以建立 Azure Key Vault 和儲存範例應用程式所使用的秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-334">The instructions provided by [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span>

1. <span data-ttu-id="3760c-335">使用 [Azure 入口網站](https://portal.azure.com/)中的下列其中一種方法開啟 Azure Cloud Shell：</span><span class="sxs-lookup"><span data-stu-id="3760c-335">Open Azure Cloud Shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="3760c-336">選取程式碼區塊右上角的 [試試看]。</span><span class="sxs-lookup"><span data-stu-id="3760c-336">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="3760c-337">在文字方塊中使用搜尋字串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="3760c-337">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="3760c-338">在您的瀏覽器中，使用 [ **啟動 Cloud Shell** ] 按鈕開啟 Cloud Shell。</span><span class="sxs-lookup"><span data-stu-id="3760c-338">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="3760c-339">選取 Azure 入口網站右上角功能表上的 **[Cloud Shell]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3760c-339">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="3760c-340">如需詳細資訊，請參閱[Azure Cloud Shell 的](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)和總覽。</span><span class="sxs-lookup"><span data-stu-id="3760c-340">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="3760c-341">如果您尚未經過驗證，請使用命令登入 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-341">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="3760c-342">使用下列命令建立資源群組，其中 `{RESOURCE GROUP NAME}` 是新的資源群組的名稱，而 `{LOCATION}` 是 Azure 區域：</span><span class="sxs-lookup"><span data-stu-id="3760c-342">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the new resource group's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3760c-343">使用下列命令在資源群組中建立金鑰保存庫，其中 `{KEY VAULT NAME}` 是新的金鑰保存庫名稱，而且 `{LOCATION}` 是 Azure 區域：</span><span class="sxs-lookup"><span data-stu-id="3760c-343">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the new key vault's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3760c-344">以名稱/值組的形式在金鑰保存庫中建立秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-344">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="3760c-345">Azure Key Vault 秘密名稱僅限英數位元和連字號。</span><span class="sxs-lookup"><span data-stu-id="3760c-345">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="3760c-346">階層值 (設定區段) 使用 `--` (兩個虛線) 作為分隔符號，因為金鑰保存庫秘密名稱中不允許有冒號。</span><span class="sxs-lookup"><span data-stu-id="3760c-346">Hierarchical values (configuration sections) use `--` (two dashes) as a delimiter, as colons aren't allowed in key vault secret names.</span></span> <span data-ttu-id="3760c-347">冒號會從 [ASP.NET Core](xref:fundamentals/configuration/index)設定中的子機碼分隔區段。</span><span class="sxs-lookup"><span data-stu-id="3760c-347">Colons delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3760c-348">將秘密載入至應用程式的設定時，會以冒號取代雙虛線序列。</span><span class="sxs-lookup"><span data-stu-id="3760c-348">The two-dash sequence is replaced with a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="3760c-349">下列秘密適用于範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-349">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="3760c-350">這些值包含後置詞 `_prod` ，以便與 `_dev` 秘密管理員從開發環境中載入的尾碼值區別。</span><span class="sxs-lookup"><span data-stu-id="3760c-350">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from Secret Manager.</span></span> <span data-ttu-id="3760c-351">將取代 `{KEY VAULT NAME}` 為您在先前步驟中建立的金鑰保存庫名稱：</span><span class="sxs-lookup"><span data-stu-id="3760c-351">Replace `{KEY VAULT NAME}` with the name of the key vault you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="3760c-352">針對非 Azure 託管應用程式使用應用程式識別碼和 x.509 憑證</span><span class="sxs-lookup"><span data-stu-id="3760c-352">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="3760c-353">設定 Azure AD、Azure Key Vault 和應用程式，以在 **應用程式裝載于 Azure 外部時**，使用 Azure AD 應用程式識別碼和 x.509 憑證來向金鑰保存庫進行驗證。</span><span class="sxs-lookup"><span data-stu-id="3760c-353">Configure Azure AD, Azure Key Vault, and the app to use an Azure AD Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="3760c-354">如需詳細資訊，請參閱[關於金鑰、祕密和憑證](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="3760c-354">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="3760c-355">雖然 Azure 中託管的應用程式支援使用應用程式識別碼和 x.509 憑證，但不建議使用。</span><span class="sxs-lookup"><span data-stu-id="3760c-355">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, it's not recommended.</span></span> <span data-ttu-id="3760c-356">請改為在 Azure 中裝載應用程式時，使用 [適用于 azure 資源的受控](#use-managed-identities-for-azure-resources) 識別。</span><span class="sxs-lookup"><span data-stu-id="3760c-356">Instead, use [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="3760c-357">受控識別不需要將憑證儲存在應用程式或開發環境中。</span><span class="sxs-lookup"><span data-stu-id="3760c-357">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="3760c-358">當 `#define` *程式 .cs* 檔案頂端的語句設定為時，範例應用程式會使用應用程式識別碼和 x.509 `Certificate` 憑證。</span><span class="sxs-lookup"><span data-stu-id="3760c-358">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="3760c-359">建立 PKCS # 12 封存 (*.pfx*) 憑證。</span><span class="sxs-lookup"><span data-stu-id="3760c-359">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="3760c-360">建立憑證的選項包括 [Windows](/windows/desktop/seccrypto/makecert) 和 [OpenSSL](https://www.openssl.org/)上的 MakeCert。</span><span class="sxs-lookup"><span data-stu-id="3760c-360">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="3760c-361">將憑證安裝到目前使用者的個人憑證存儲中。</span><span class="sxs-lookup"><span data-stu-id="3760c-361">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="3760c-362">將金鑰標示為可匯出是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="3760c-362">Marking the key as exportable is optional.</span></span> <span data-ttu-id="3760c-363">請注意憑證的指紋，此憑證稍後會在此程式中使用。</span><span class="sxs-lookup"><span data-stu-id="3760c-363">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="3760c-364">將 PKCS # 12 封存 (*.pfx*) 憑證匯出為 DER 編碼憑證 (*.cer*) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-364">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="3760c-365">使用 Azure AD (**應用程式註冊**) 註冊應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-365">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="3760c-366">將 DER 編碼憑證 (*.cer*) 上傳至 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="3760c-366">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="3760c-367">在 Azure AD 中選取應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-367">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="3760c-368">流覽至 **憑證 & 秘密**。</span><span class="sxs-lookup"><span data-stu-id="3760c-368">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="3760c-369">選取 [ **上傳憑證** ] 以上傳包含公開金鑰的憑證。</span><span class="sxs-lookup"><span data-stu-id="3760c-369">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="3760c-370">*.Cer*、 *pem* 或 *.crt* 憑證是可接受的。</span><span class="sxs-lookup"><span data-stu-id="3760c-370">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="3760c-371">將金鑰保存庫名稱、應用程式識別碼和憑證指紋儲存在應用程式的檔案中 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3760c-371">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="3760c-372">流覽至 Azure 入口網站中的 **金鑰保存庫** 。</span><span class="sxs-lookup"><span data-stu-id="3760c-372">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="3760c-373">使用 Azure Key Vault 區段，選取您在 [生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中建立的金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-373">Select the key vault you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="3760c-374">選取 [存取原則]。</span><span class="sxs-lookup"><span data-stu-id="3760c-374">Select **Access policies**.</span></span>
1. <span data-ttu-id="3760c-375">選取 [新增存取原則]。</span><span class="sxs-lookup"><span data-stu-id="3760c-375">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="3760c-376">開啟 **秘密許可權** ，並提供具有 **取得** 和 **列出** 許可權的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-376">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="3760c-377">選取 [ **選取主體** ]，然後依名稱選取已註冊的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-377">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="3760c-378">選取 [選取]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="3760c-378">Select the **Select** button.</span></span>
1. <span data-ttu-id="3760c-379">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="3760c-379">Select **OK**.</span></span>
1. <span data-ttu-id="3760c-380">選取 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="3760c-380">Select **Save**.</span></span>
1. <span data-ttu-id="3760c-381">部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="3760c-381">Deploy the app.</span></span>

<span data-ttu-id="3760c-382">`Certificate`範例應用程式會從 `IConfigurationRoot` 與秘密名稱相同的名稱取得其設定值：</span><span class="sxs-lookup"><span data-stu-id="3760c-382">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="3760c-383">非階層式值：的值 `SecretName` 是使用取得 `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-383">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="3760c-384">階層值 (區段) ：使用 `:` (冒號) 標記法或 `GetSection` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="3760c-384">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="3760c-385">您可以使用下列其中一種方法來取得設定值：</span><span class="sxs-lookup"><span data-stu-id="3760c-385">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="3760c-386">X.509 憑證由作業系統管理。</span><span class="sxs-lookup"><span data-stu-id="3760c-386">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="3760c-387">應用程式會 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 使用檔案所提供的值來呼叫 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="3760c-387">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="3760c-388">範例值：</span><span class="sxs-lookup"><span data-stu-id="3760c-388">Example values:</span></span>

* <span data-ttu-id="3760c-389">金鑰保存庫名稱： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="3760c-389">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="3760c-390">應用程式識別碼： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="3760c-390">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="3760c-391">憑證指紋： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="3760c-391">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="3760c-392">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3760c-392">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="3760c-393">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-393">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3760c-394">在開發環境中，秘密值會以 `_dev` 尾碼載入。</span><span class="sxs-lookup"><span data-stu-id="3760c-394">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="3760c-395">在生產環境中，值會以後綴載入 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-395">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="3760c-396">使用適用於 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="3760c-396">Use managed identities for Azure resources</span></span>

<span data-ttu-id="3760c-397">**部署至 azure 的應用程式** 可以利用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別。</span><span class="sxs-lookup"><span data-stu-id="3760c-397">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview).</span></span> <span data-ttu-id="3760c-398">受控識別可讓應用程式使用 Azure AD 驗證來驗證 Azure Key Vault，而不需要認證 (應用程式識別碼和密碼/用戶端秘密) 儲存在應用程式中。</span><span class="sxs-lookup"><span data-stu-id="3760c-398">A managed identity allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="3760c-399">當 `#define` *程式 .cs* 檔案頂端的語句設定為時，範例應用程式會使用適用于 Azure 資源的受控識別 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-399">The sample app uses managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="3760c-400">將保存庫名稱輸入應用程式的檔案 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3760c-400">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="3760c-401">範例應用程式在設定為版本時，不需要 (用戶端密碼) 的應用程式識別碼和密碼 `Managed` ，因此您可以忽略這些設定專案。</span><span class="sxs-lookup"><span data-stu-id="3760c-401">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="3760c-402">應用程式會部署至 Azure，而 Azure 會使用儲存在檔案中的保存庫名稱來驗證應用程式，以存取 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3760c-402">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="3760c-403">將範例應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="3760c-403">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="3760c-404">部署到 Azure App Service 的應用程式會在建立服務時自動向 Azure AD 註冊。</span><span class="sxs-lookup"><span data-stu-id="3760c-404">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="3760c-405">從部署中取得物件識別碼，以在下列命令中使用。</span><span class="sxs-lookup"><span data-stu-id="3760c-405">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="3760c-406">物件識別碼會顯示在 App Service 面板的 [Azure 入口網站] 中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="3760c-406">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="3760c-407">使用 Azure CLI 和應用程式的物件識別碼，為應用程式提供 `list` `get` 存取金鑰保存庫的和許可權：</span><span class="sxs-lookup"><span data-stu-id="3760c-407">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="3760c-408">使用 Azure CLI、PowerShell 或 Azure 入口網站 **重新開機應用程式**。</span><span class="sxs-lookup"><span data-stu-id="3760c-408">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="3760c-409">範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="3760c-409">The sample app:</span></span>

* <span data-ttu-id="3760c-410">建立 `AzureServiceTokenProvider` 沒有連接字串之類別的實例。</span><span class="sxs-lookup"><span data-stu-id="3760c-410">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="3760c-411">未提供連接字串時，提供者會嘗試從 Azure 資源的受控識別取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="3760c-411">When a connection string isn't provided, the provider attempts to obtain an access token from managed identities for Azure resources.</span></span>
* <span data-ttu-id="3760c-412"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 實例權杖回呼建立新的。</span><span class="sxs-lookup"><span data-stu-id="3760c-412">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="3760c-413">此 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 實例會與的預設執行一起使用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，以載入所有的秘密值，並以冒號取代雙虛線 (`--`)  (`:`) 的索引鍵名稱。</span><span class="sxs-lookup"><span data-stu-id="3760c-413">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="3760c-414">金鑰保存庫名稱範例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="3760c-414">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="3760c-415">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3760c-415">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="3760c-416">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-416">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3760c-417">在開發環境中，秘密值具有 `_dev` 尾碼，因為它們是由秘密管理員所提供。</span><span class="sxs-lookup"><span data-stu-id="3760c-417">In the Development environment, secret values have the `_dev` suffix because they're provided by Secret Manager.</span></span> <span data-ttu-id="3760c-418">在生產環境中，值會以後綴載入， `_prod` 因為它們是由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="3760c-418">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="3760c-419">如果您收到 `Access denied` 錯誤，請確認已向 Azure AD 註冊應用程式，並提供金鑰保存庫的存取權。</span><span class="sxs-lookup"><span data-stu-id="3760c-419">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="3760c-420">確認您已在 Azure 中重新開機服務。</span><span class="sxs-lookup"><span data-stu-id="3760c-420">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="3760c-421">如需搭配受控識別和 Azure Pipelines 使用提供者的詳細資訊，請參閱使用 [受控服務識別建立與 VM 的 Azure Resource Manager 服務](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)連線。</span><span class="sxs-lookup"><span data-stu-id="3760c-421">For information on using the provider with a managed identity and Azure Pipelines, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="3760c-422">使用金鑰名稱前置詞</span><span class="sxs-lookup"><span data-stu-id="3760c-422">Use a key name prefix</span></span>

<span data-ttu-id="3760c-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 提供可接受之執行的多載 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。</span><span class="sxs-lookup"><span data-stu-id="3760c-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>.</span></span> <span data-ttu-id="3760c-424">此多載可讓您控制如何將金鑰保存庫密碼轉換成設定金鑰。</span><span class="sxs-lookup"><span data-stu-id="3760c-424">This overload allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="3760c-425">例如，您可以根據您在應用程式啟動時所提供的前置詞值，執行介面以載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-425">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="3760c-426">例如，這項技術可讓您根據應用程式的版本載入秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-426">This technique allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="3760c-427">請勿在金鑰保存庫秘密上使用前置詞，將多個應用程式的秘密放入相同的金鑰保存庫中，或將環境秘密 (例如， *開發* 和 *生產* 秘密) 至相同的保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-427">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="3760c-428">建議不同的應用程式和開發/生產環境使用不同的金鑰保存庫，以隔離最高層級安全性的應用程式環境。</span><span class="sxs-lookup"><span data-stu-id="3760c-428">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="3760c-429">下列範例會在金鑰保存庫中建立秘密 (並且針對開發環境使用秘密管理員) `5000-AppSecret` (期間不允許金鑰保存庫秘密名稱) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-429">In the following example, a secret is established in the key vault (and using Secret Manager for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="3760c-430">此秘密代表應用程式版本5.0.0.0 的應用程式密碼。</span><span class="sxs-lookup"><span data-stu-id="3760c-430">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="3760c-431">針對另一個版本的應用程式，5.1.0.0 會將秘密新增至金鑰保存庫 (並使用的秘密管理員) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-431">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using Secret Manager) for `5100-AppSecret`.</span></span> <span data-ttu-id="3760c-432">每個應用程式版本都會將其已建立版本的秘密值載入至其設定 `AppSecret` 中，以在載入密碼時移除版本。</span><span class="sxs-lookup"><span data-stu-id="3760c-432">Each app version loads its versioned secret value into its configuration as `AppSecret`, removing the version as it loads the secret.</span></span>

<span data-ttu-id="3760c-433"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 使用自訂來呼叫 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="3760c-433"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="3760c-434">此 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 執行會回應秘密的版本前置詞，以將適當的秘密載入設定：</span><span class="sxs-lookup"><span data-stu-id="3760c-434">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="3760c-435">`Load` 在名稱開頭為前置詞時載入秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-435">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="3760c-436">未載入其他秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-436">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="3760c-437">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="3760c-437">`GetKey`:</span></span>
  * <span data-ttu-id="3760c-438">移除秘密名稱中的前置詞。</span><span class="sxs-lookup"><span data-stu-id="3760c-438">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="3760c-439">使用的任何名稱取代兩個虛線 `KeyDelimiter` ，也就是設定 (中使用的分隔符號，通常是) 的冒號。</span><span class="sxs-lookup"><span data-stu-id="3760c-439">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="3760c-440">Azure Key Vault 不允許秘密名稱中有冒號。</span><span class="sxs-lookup"><span data-stu-id="3760c-440">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/PrefixKeyVaultSecretManager.cs)]

<span data-ttu-id="3760c-441">`Load`方法是由提供者演算法呼叫，此演算法會逐一查看保存庫秘密，以找出具有版本首碼的金鑰。</span><span class="sxs-lookup"><span data-stu-id="3760c-441">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="3760c-442">當找到版本首碼時 `Load` ，演算法會使用 `GetKey` 方法來傳回秘密名稱的設定名稱。</span><span class="sxs-lookup"><span data-stu-id="3760c-442">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="3760c-443">它會移除秘密名稱的版本前置詞。</span><span class="sxs-lookup"><span data-stu-id="3760c-443">It removes the version prefix from the secret's name.</span></span> <span data-ttu-id="3760c-444">會傳回秘密名稱的其餘部分，以供載入至應用程式的設定名稱-值配對。</span><span class="sxs-lookup"><span data-stu-id="3760c-444">The rest of the secret name is returned for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="3760c-445">當此方法實施時：</span><span class="sxs-lookup"><span data-stu-id="3760c-445">When this approach is implemented:</span></span>

1. <span data-ttu-id="3760c-446">應用程式的專案檔中所指定的應用程式版本。</span><span class="sxs-lookup"><span data-stu-id="3760c-446">The app's version specified in the app's project file.</span></span> <span data-ttu-id="3760c-447">在下列範例中，應用程式的版本會設定為 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="3760c-447">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="3760c-448">確認 `<UserSecretsId>` 屬性存在於應用程式的專案檔中，其中 `{GUID}` 是使用者提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="3760c-448">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="3760c-449">使用 [秘密管理員](xref:security/app-secrets)在本機儲存下列秘密：</span><span class="sxs-lookup"><span data-stu-id="3760c-449">Save the following secrets locally with [Secret Manager](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="3760c-450">使用下列 Azure CLI 命令，將秘密儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="3760c-450">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="3760c-451">執行應用程式時，會載入金鑰保存庫秘密。</span><span class="sxs-lookup"><span data-stu-id="3760c-451">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="3760c-452">的字串秘密 `5000-AppSecret` 會與應用程式專案檔中所指定的應用程式版本相符 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-452">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="3760c-453">`5000`以虛線)  (的版本會從索引鍵名稱中移除。</span><span class="sxs-lookup"><span data-stu-id="3760c-453">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="3760c-454">在整個應用程式中，使用金鑰讀取設定會 `AppSecret` 載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="3760c-454">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="3760c-455">如果將專案檔中的應用程式版本變更為 `5.1.0.0` ，並再次執行應用程式，則傳回的秘密值會 `5.1.0.0_secret_value_dev` 在開發環境和生產環境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-455">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="3760c-456">您也可以提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 實作為 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 。</span><span class="sxs-lookup"><span data-stu-id="3760c-456">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A>.</span></span> <span data-ttu-id="3760c-457">自訂用戶端允許跨應用程式共用用戶端的單一實例。</span><span class="sxs-lookup"><span data-stu-id="3760c-457">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="3760c-458">將陣列繫結到類別</span><span class="sxs-lookup"><span data-stu-id="3760c-458">Bind an array to a class</span></span>

<span data-ttu-id="3760c-459">提供者可以將設定值讀取至陣列，以系結至 POCO 陣列。</span><span class="sxs-lookup"><span data-stu-id="3760c-459">The provider can read configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="3760c-460">從允許金鑰包含冒號 () 分隔符號的設定來源進行讀取時 `:` ，會使用數值索引鍵區段來區分組成陣列的索引鍵 (`:0:` 、 `:1:` 、 &hellip; `:{n}:`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-460">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="3760c-461">如需詳細資訊，請參閱設定：將陣列系結 [至類別](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="3760c-461">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="3760c-462">Azure Key Vault 金鑰不能使用冒號做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="3760c-462">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="3760c-463">本檔中所述的方法會使用雙虛線 (`--`) 做為階層值的分隔符號 (區段) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-463">The approach described in this document uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="3760c-464">陣列索引鍵會儲存在 Azure Key Vault 中，並以雙連字號和數位鍵區段 (`--0--` 、 `--1--` &hellip; `--{n}--`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-464">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="3760c-465">檢查 JSON 檔案所提供的下列 [Serilog](https://serilog.net/) 記錄提供者設定。</span><span class="sxs-lookup"><span data-stu-id="3760c-465">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="3760c-466">陣列中定義了兩個物件常值 `WriteTo` ，它會反映兩個 Serilog *接收*，其描述記錄輸出的目的地：</span><span class="sxs-lookup"><span data-stu-id="3760c-466">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="3760c-467">先前 JSON 檔案中顯示的設定會使用雙虛線 (`--`) 標記法和數位區段，儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="3760c-467">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="3760c-468">Key</span><span class="sxs-lookup"><span data-stu-id="3760c-468">Key</span></span> | <span data-ttu-id="3760c-469">值</span><span class="sxs-lookup"><span data-stu-id="3760c-469">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="3760c-470">重載秘密</span><span class="sxs-lookup"><span data-stu-id="3760c-470">Reload secrets</span></span>

<span data-ttu-id="3760c-471">在呼叫之前，會快取秘密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-471">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="3760c-472">在執行之前，應用程式不會遵守金鑰保存庫中已過期、已停用和更新的秘密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="3760c-472">Expired, disabled, and updated secrets in the key vault aren't respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="3760c-473">已停用和過期的秘密</span><span class="sxs-lookup"><span data-stu-id="3760c-473">Disabled and expired secrets</span></span>

<span data-ttu-id="3760c-474">停用和過期的秘密會擲回 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="3760c-474">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="3760c-475">若要防止應用程式擲回，請使用不同的設定提供者來提供設定，或更新已停用或過期的密碼。</span><span class="sxs-lookup"><span data-stu-id="3760c-475">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3760c-476">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3760c-476">Troubleshoot</span></span>

<span data-ttu-id="3760c-477">當應用程式無法使用提供者載入設定時，會將錯誤訊息寫入 [ASP.NET Core 記錄基礎結構](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="3760c-477">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3760c-478">下列情況會導致無法載入設定：</span><span class="sxs-lookup"><span data-stu-id="3760c-478">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="3760c-479">Azure AD 中的應用程式或憑證未正確設定。</span><span class="sxs-lookup"><span data-stu-id="3760c-479">The app or certificate isn't configured correctly in Azure AD.</span></span>
* <span data-ttu-id="3760c-480">金鑰保存庫不存在於 Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="3760c-480">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="3760c-481">應用程式未獲授權存取金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="3760c-481">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="3760c-482">存取原則不包含 `Get` 和 `List` 許可權。</span><span class="sxs-lookup"><span data-stu-id="3760c-482">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="3760c-483">在金鑰保存庫中，設定資料 (的名稱/值組) 不正確地命名、遺失、停用或過期。</span><span class="sxs-lookup"><span data-stu-id="3760c-483">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="3760c-484">應用程式有錯誤的金鑰保存庫名稱 (`KeyVaultName`) 、Azure AD 應用程式識別碼 (`AzureADApplicationId`) ，或 Azure AD 憑證指紋 (`AzureADCertThumbprint`) 。</span><span class="sxs-lookup"><span data-stu-id="3760c-484">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application ID (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="3760c-485">在您嘗試載入的值的應用程式中，設定機碼 (名稱) 不正確。</span><span class="sxs-lookup"><span data-stu-id="3760c-485">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="3760c-486">將應用程式的存取原則新增至金鑰保存庫時，已建立原則，但未在 [**存取原則**] UI 中選取 [**儲存**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3760c-486">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3760c-487">其他資源</span><span class="sxs-lookup"><span data-stu-id="3760c-487">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="3760c-488">Microsoft Azure： Key Vault 檔</span><span class="sxs-lookup"><span data-stu-id="3760c-488">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="3760c-489">如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰</span><span class="sxs-lookup"><span data-stu-id="3760c-489">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="3760c-490">快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密</span><span class="sxs-lookup"><span data-stu-id="3760c-490">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="3760c-491">教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Windows 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="3760c-491">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
