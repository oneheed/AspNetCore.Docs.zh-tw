---
title: ASP.NET Core 中的 Azure Key Vault 設定提供者
author: rick-anderson
description: 瞭解如何使用 Azure 金鑰保存庫設定提供者，以使用在執行時間載入的名稱/值組來設定應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
ms.openlocfilehash: ab3a462a3e09113e96c6bdd0c034bff3e0bebdfa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588291"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="d19ce-103">ASP.NET Core 中的 Azure Key Vault 設定提供者</span><span class="sxs-lookup"><span data-stu-id="d19ce-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="d19ce-104">[Andrew Stanton-護士](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d19ce-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d19ce-105">本檔說明如何使用 [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/) 設定提供者，從 Azure key vault 秘密載入應用程式設定值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="d19ce-106">Azure Key Vault 是一項雲端式服務，可協助保護應用程式和服務所使用的密碼編譯金鑰和秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="d19ce-107">搭配使用 Azure Key Vault 與 ASP.NET Core 應用程式的常見案例包括：</span><span class="sxs-lookup"><span data-stu-id="d19ce-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="d19ce-108">控制對敏感設定資料的存取。</span><span class="sxs-lookup"><span data-stu-id="d19ce-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="d19ce-109">在儲存設定資料時，符合 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 的需求。</span><span class="sxs-lookup"><span data-stu-id="d19ce-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="d19ce-110">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d19ce-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="d19ce-111">套件</span><span class="sxs-lookup"><span data-stu-id="d19ce-111">Packages</span></span>

<span data-ttu-id="d19ce-112">新增下列封裝的套件參考：</span><span class="sxs-lookup"><span data-stu-id="d19ce-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="d19ce-113">Azure.Extensions.AspNetCore.Configuration。秘密</span><span class="sxs-lookup"><span data-stu-id="d19ce-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="d19ce-114">[蔚藍。Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="d19ce-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="d19ce-115">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="d19ce-115">Sample app</span></span>

<span data-ttu-id="d19ce-116">範例應用程式會以 Program.cs 檔頂端的語句所決定的兩種模式之一執行 `#define` ： </span><span class="sxs-lookup"><span data-stu-id="d19ce-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="d19ce-117">`Certificate`：示範如何使用 Azure 金鑰保存庫用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="d19ce-118">此版本的範例可從任何位置執行，其部署至 Azure App Service 或任何能夠提供 ASP.NET Core 應用程式的主機。</span><span class="sxs-lookup"><span data-stu-id="d19ce-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="d19ce-119">`Managed`：示範如何使用 azure [資源的受控](/azure/active-directory/managed-identities-azure-resources/overview) 識別，透過 azure AD 驗證來驗證 Azure Key Vault 中的應用程式，而不需將認證儲存在應用程式的程式碼或設定中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="d19ce-120">使用受控識別進行驗證時，不需要 Azure AD 應用程式識別碼和密碼 (用戶端密碼) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="d19ce-121">`Managed`範例的版本必須部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="d19ce-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="d19ce-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span><span class="sxs-lookup"><span data-stu-id="d19ce-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="d19ce-123">如需如何使用預處理器指示詞 () 設定範例應用程式的詳細資訊 `#define` ，請參閱 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="d19ce-124">開發環境中的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="d19ce-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="d19ce-125">使用 [秘密管理員工具](xref:security/app-secrets)在本機設定秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d19ce-126">當範例應用程式在開發環境中的本機電腦上執行時，會從本機使用者秘密存放區載入秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="d19ce-127">秘密管理員工具需要 `<UserSecretsId>` 應用程式專案檔案中的屬性。</span><span class="sxs-lookup"><span data-stu-id="d19ce-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="d19ce-128">將屬性值 (`{GUID}`) 設定為任何唯一的 GUID：</span><span class="sxs-lookup"><span data-stu-id="d19ce-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="d19ce-129">秘密會以名稱/值組的形式建立。</span><span class="sxs-lookup"><span data-stu-id="d19ce-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="d19ce-130">階層值 (設定區段) 使用 `:` (冒號) 作為 [ASP.NET 核心](xref:fundamentals/configuration/index) 設定機碼名稱中的分隔符號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="d19ce-131">秘密管理員是從開啟至專案 [內容根目錄](xref:fundamentals/index#content-root)的命令 shell 使用，其中 `{SECRET NAME}` 是名稱，而 `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="d19ce-132">從專案的 [內容根目錄](xref:fundamentals/index#content-root) 在命令 shell 中執行下列命令，以設定範例應用程式的密碼：</span><span class="sxs-lookup"><span data-stu-id="d19ce-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="d19ce-133">當這些秘密儲存在 Azure Key Vault 中， [並使用 Azure Key vault 區段在生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 時， `_dev` 尾碼會變更為 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="d19ce-134">尾碼會在應用程式的輸出中提供視覺提示，指出設定值的來源。</span><span class="sxs-lookup"><span data-stu-id="d19ce-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="d19ce-135">在生產環境中使用 Azure Key Vault 的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="d19ce-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="d19ce-136">[快速入門：使用 AZURE CLI 從 Azure Key Vault 設定及取出秘密](/azure/key-vault/quick-create-cli)的說明，在此摘要說明如何建立 azure 金鑰保存庫，並儲存範例應用程式所使用的秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="d19ce-137">如需進一步的詳細資料，請參閱主題。</span><span class="sxs-lookup"><span data-stu-id="d19ce-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="d19ce-138">在 [azure 入口網站](https://portal.azure.com/)中使用下列任何一種方法來開啟 azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="d19ce-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="d19ce-139">選取程式碼區塊右上角的 [試試看]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="d19ce-140">在文字方塊中使用搜尋字串「Azure CLI」。</span><span class="sxs-lookup"><span data-stu-id="d19ce-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="d19ce-141">使用 [ **啟動 Cloud shell** ] 按鈕，在瀏覽器中開啟 cloud shell。</span><span class="sxs-lookup"><span data-stu-id="d19ce-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="d19ce-142">選取 Azure 入口網站右上角功能表上的 **[Cloud Shell]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d19ce-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="d19ce-143">如需詳細資訊，請參閱 [AZURE CLI](/cli/azure/) 和 [azure Cloud Shell 的總覽](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="d19ce-144">如果您尚未經過驗證，請使用命令登入 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="d19ce-145">使用下列命令建立資源群組，其中 `{RESOURCE GROUP NAME}` 是新資源群組的資源組名，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d19ce-146">使用下列命令在資源群組中建立金鑰保存庫，其中 `{KEY VAULT NAME}` 是新金鑰保存庫的名稱，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d19ce-147">以名稱/值組的形式在金鑰保存庫中建立秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="d19ce-148">Azure Key Vault 秘密名稱僅限英數位元和連字號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="d19ce-149">階層值 (設定區段) 使用 `--` (兩個虛線) 作為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d19ce-150">金鑰保存庫秘密名稱中不允許使用冒號（通常用來分隔區段的 [ASP.NET 核心](xref:fundamentals/configuration/index)設定中的子機碼）。</span><span class="sxs-lookup"><span data-stu-id="d19ce-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="d19ce-151">因此，當秘密載入至應用程式的設定時，會使用兩個連字號，並將其交換為冒號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="d19ce-152">下列秘密適用于範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="d19ce-153">這些值包含後置詞 `_prod` ，可區別這些值與 `_dev` 開發環境中從使用者秘密載入的尾碼值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="d19ce-154">將取代 `{KEY VAULT NAME}` 為您在先前步驟中建立的金鑰保存庫名稱：</span><span class="sxs-lookup"><span data-stu-id="d19ce-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="d19ce-155">針對非 Azure 託管應用程式使用應用程式識別碼和 x.509 憑證</span><span class="sxs-lookup"><span data-stu-id="d19ce-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="d19ce-156">設定 Azure AD、Azure 金鑰保存庫和應用程式，以使用 Azure Active Directory 應用程式識別碼和 x.509 憑證來向金鑰保存庫進行驗證 **（當應用程式裝載于 Azure 之外**）。</span><span class="sxs-lookup"><span data-stu-id="d19ce-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="d19ce-157">如需詳細資訊，請參閱[關於金鑰、祕密和憑證](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="d19ce-158">雖然 Azure 中裝載的應用程式支援使用應用程式識別碼和 x.509 憑證，但我們建議在 azure 中裝載應用程式時，使用 [適用于 azure 資源的受控](#use-managed-identities-for-azure-resources) 識別。</span><span class="sxs-lookup"><span data-stu-id="d19ce-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="d19ce-159">受控識別不需要將憑證儲存在應用程式或開發環境中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="d19ce-160">當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用應用程式識別碼和 x.509 憑證 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="d19ce-161">建立 PKCS # 12 封存 (*.pfx*) 憑證。</span><span class="sxs-lookup"><span data-stu-id="d19ce-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="d19ce-162">建立憑證的選項包括 [Windows](/windows/desktop/seccrypto/makecert) 和 [OpenSSL](https://www.openssl.org/)上的 MakeCert。</span><span class="sxs-lookup"><span data-stu-id="d19ce-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="d19ce-163">將憑證安裝到目前使用者的個人憑證存儲中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="d19ce-164">將金鑰標示為可匯出是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="d19ce-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="d19ce-165">請注意憑證的指紋，此憑證稍後會在此程式中使用。</span><span class="sxs-lookup"><span data-stu-id="d19ce-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="d19ce-166">將 PKCS # 12 封存 (*.pfx*) 憑證匯出為 DER 編碼憑證 (*.cer*) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="d19ce-167">使用 Azure AD (**應用程式** 註冊) 註冊應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="d19ce-168">將 DER 編碼憑證 (*.cer*) 上傳至 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="d19ce-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="d19ce-169">在 Azure AD 中選取應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="d19ce-170">流覽至 **憑證 & 秘密**。</span><span class="sxs-lookup"><span data-stu-id="d19ce-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="d19ce-171">選取 [ **上傳憑證** ] 以上傳包含公開金鑰的憑證。</span><span class="sxs-lookup"><span data-stu-id="d19ce-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="d19ce-172">*.Cer*、 *pem* 或 *.crt* 憑證是可接受的。</span><span class="sxs-lookup"><span data-stu-id="d19ce-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="d19ce-173">將金鑰保存庫名稱、應用程式識別碼和憑證指紋儲存在應用程式的檔案中 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="d19ce-174">在 Azure 入口網站中流覽至 **金鑰保存庫** 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="d19ce-175">使用 Azure Key Vault 區段，選取您在 [生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中建立的金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="d19ce-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="d19ce-176">選取 [存取原則]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="d19ce-177">選取 [新增存取原則]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="d19ce-178">開啟 **秘密許可權** ，並提供具有 **取得** 和 **列出** 許可權的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="d19ce-179">選取 [ **選取主體** ]，然後依名稱選取已註冊的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="d19ce-180">選取 [選取]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="d19ce-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="d19ce-181">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-181">Select **OK**.</span></span>
1. <span data-ttu-id="d19ce-182">選取 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-182">Select **Save**.</span></span>
1. <span data-ttu-id="d19ce-183">部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-183">Deploy the app.</span></span>

<span data-ttu-id="d19ce-184">`Certificate`範例應用程式會從 `IConfigurationRoot` 與秘密名稱相同的名稱取得其設定值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="d19ce-185">非階層式值：的值 `SecretName` 是使用取得 `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="d19ce-186">階層值 (區段) ：使用 `:` (冒號) 標記法或 `GetSection` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d19ce-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="d19ce-187">您可以使用下列其中一種方法來取得設定值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="d19ce-188">X.509 憑證由作業系統管理。</span><span class="sxs-lookup"><span data-stu-id="d19ce-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="d19ce-189">應用程式會使用檔案所提供的值來呼叫 **瞭解 addazurekeyvault** *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="d19ce-190">範例值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-190">Example values:</span></span>

* <span data-ttu-id="d19ce-191">金鑰保存庫名稱： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d19ce-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="d19ce-192">應用程式識別碼： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="d19ce-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="d19ce-193">憑證指紋： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="d19ce-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="d19ce-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d19ce-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="d19ce-195">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d19ce-196">在開發環境中，秘密值會以 `_dev` 尾碼載入。</span><span class="sxs-lookup"><span data-stu-id="d19ce-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="d19ce-197">在生產環境中，值會以後綴載入 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="d19ce-198">使用適用于 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="d19ce-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="d19ce-199">**部署至 azure 的應用程式** 可以利用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別，讓應用程式使用 azure AD 驗證來驗證 azure Key Vault，而不需要認證 (應用程式識別碼和密碼/用戶端密碼) 儲存在應用程式中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="d19ce-200">當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用適用于 Azure 資源的受控識別 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="d19ce-201">將保存庫名稱輸入應用程式的檔案 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="d19ce-202">範例應用程式在設定為版本時，不需要 (用戶端密碼) 的應用程式識別碼和密碼 `Managed` ，因此您可以忽略這些設定專案。</span><span class="sxs-lookup"><span data-stu-id="d19ce-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="d19ce-203">應用程式會部署至 Azure，而 Azure 會使用儲存在檔案中的保存庫名稱來驗證應用程式，以存取 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="d19ce-204">將範例應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="d19ce-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="d19ce-205">部署至 Azure App Service 的應用程式會在建立服務時自動向 Azure AD 註冊。</span><span class="sxs-lookup"><span data-stu-id="d19ce-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="d19ce-206">從部署中取得物件識別碼，以在下列命令中使用。</span><span class="sxs-lookup"><span data-stu-id="d19ce-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="d19ce-207">物件識別碼會顯示在 Azure 入口網站中 **Identity** App Service 的面板上。</span><span class="sxs-lookup"><span data-stu-id="d19ce-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="d19ce-208">使用 Azure CLI 和應用程式的物件識別碼，為應用程式提供 `list` `get` 存取金鑰保存庫的和許可權：</span><span class="sxs-lookup"><span data-stu-id="d19ce-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="d19ce-209">使用 Azure CLI、PowerShell 或 Azure 入口網站 **重新開機應用程式**。</span><span class="sxs-lookup"><span data-stu-id="d19ce-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="d19ce-210">範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="d19ce-210">The sample app:</span></span>

* <span data-ttu-id="d19ce-211">建立類別的實例 `DefaultAzureCredential` ，認證會嘗試從 Azure 資源的環境取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="d19ce-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="d19ce-212">[`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets)使用實例建立新的 `DefaultAzureCredential` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="d19ce-213">此 `Azure.Security.KeyVault.Secrets.Secrets` 實例會與的預設執行一起使用 `Azure.Extensions.AspNetCore.Configuration.Secrets` ，以載入所有的秘密值，並以冒號取代雙虛線 (`--`)  (`:`) 的索引鍵名稱。</span><span class="sxs-lookup"><span data-stu-id="d19ce-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="d19ce-214">金鑰保存庫名稱範例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d19ce-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="d19ce-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d19ce-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="d19ce-216">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d19ce-217">在開發環境中，秘密值具有 `_dev` 尾碼，因為它們是由使用者密碼提供。</span><span class="sxs-lookup"><span data-stu-id="d19ce-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="d19ce-218">在生產環境中，值會以後綴載入， `_prod` 因為它們是由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="d19ce-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="d19ce-219">如果您收到 `Access denied` 錯誤，請確認應用程式已向 AZURE AD 註冊，並已提供金鑰保存庫的存取權。</span><span class="sxs-lookup"><span data-stu-id="d19ce-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="d19ce-220">確認您已在 Azure 中重新開機服務。</span><span class="sxs-lookup"><span data-stu-id="d19ce-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="d19ce-221">如需搭配受控識別和 Azure DevOps 管線使用提供者的詳細資訊，請參閱使用 [受控服務識別建立與 VM 的 Azure Resource Manager 服務](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)連線。</span><span class="sxs-lookup"><span data-stu-id="d19ce-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="d19ce-222">設定選項</span><span class="sxs-lookup"><span data-stu-id="d19ce-222">Configuration options</span></span>

<span data-ttu-id="d19ce-223">瞭解 addazurekeyvault 可以接受 AzureKeyVaultConfigurationOptions：</span><span class="sxs-lookup"><span data-stu-id="d19ce-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="d19ce-224">屬性</span><span class="sxs-lookup"><span data-stu-id="d19ce-224">Property</span></span>         | <span data-ttu-id="d19ce-225">描述</span><span class="sxs-lookup"><span data-stu-id="d19ce-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="d19ce-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` 用來控制秘密載入的實例。</span><span class="sxs-lookup"><span data-stu-id="d19ce-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="d19ce-227">`Timespan` 在輪詢金鑰保存庫以進行變更的嘗試之間等候。</span><span class="sxs-lookup"><span data-stu-id="d19ce-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="d19ce-228">預設值為 `null` (設定) 不會重載。</span><span class="sxs-lookup"><span data-stu-id="d19ce-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="d19ce-229">使用金鑰名稱前置詞</span><span class="sxs-lookup"><span data-stu-id="d19ce-229">Use a key name prefix</span></span>

<span data-ttu-id="d19ce-230">瞭解 addazurekeyvault 會提供接受執行的多載 `Azure.Extensions.AspNetCore.Configuration.Secrets` ，可讓您控制如何將金鑰保存庫密碼轉換成設定金鑰。</span><span class="sxs-lookup"><span data-stu-id="d19ce-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="d19ce-231">例如，您可以根據您在應用程式啟動時所提供的前置詞值，執行介面以載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="d19ce-232">例如，這可讓您根據應用程式的版本載入秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="d19ce-233">請勿在金鑰保存庫秘密上使用前置詞，將多個應用程式的秘密放入相同的金鑰保存庫中，或將環境秘密 (例如， *開發* 和 *生產* 秘密) 至相同的保存庫。</span><span class="sxs-lookup"><span data-stu-id="d19ce-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="d19ce-234">建議不同的應用程式和開發/生產環境使用不同的金鑰保存庫，以隔離最高層級安全性的應用程式環境。</span><span class="sxs-lookup"><span data-stu-id="d19ce-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="d19ce-235">下列範例會在金鑰保存庫中建立秘密 (並且針對開發環境使用 Secret Manager 工具) `5000-AppSecret` 不允許在金鑰保存庫秘密名稱) 中使用 (期間。</span><span class="sxs-lookup"><span data-stu-id="d19ce-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="d19ce-236">此秘密代表應用程式版本5.0.0.0 的應用程式密碼。</span><span class="sxs-lookup"><span data-stu-id="d19ce-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="d19ce-237">針對另一個版本的應用程式，5.1.0.0 會將秘密新增至金鑰保存庫 (，並使用) 的「秘密管理員」工具 `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="d19ce-238">每個應用程式版本都會將其已建立版本的秘密值載入至其設定中，並在 `AppSecret` 載入密碼時將其移除。</span><span class="sxs-lookup"><span data-stu-id="d19ce-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="d19ce-239">使用自訂來呼叫瞭解 addazurekeyvault `Azure.Extensions.AspNetCore.Configuration.Secrets` ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="d19ce-240">此 `Azure.Extensions.AspNetCore.Configuration.Secrets` 執行會回應秘密的版本前置詞，以將適當的秘密載入設定：</span><span class="sxs-lookup"><span data-stu-id="d19ce-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="d19ce-241">`Load` 在名稱開頭為前置詞時載入秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="d19ce-242">未載入其他秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="d19ce-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="d19ce-243">`GetKey`:</span></span>
  * <span data-ttu-id="d19ce-244">移除秘密名稱中的前置詞。</span><span class="sxs-lookup"><span data-stu-id="d19ce-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="d19ce-245">使用的任何名稱取代兩個虛線 `KeyDelimiter` ，也就是設定 (中使用的分隔符號，通常是) 的冒號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="d19ce-246">Azure Key Vault 不允許秘密名稱中有冒號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="d19ce-247">`Load`方法是由提供者演算法呼叫，此演算法會逐一查看保存庫秘密，以找出具有版本首碼的金鑰。</span><span class="sxs-lookup"><span data-stu-id="d19ce-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="d19ce-248">當找到版本首碼時 `Load` ，演算法會使用 `GetKey` 方法來傳回秘密名稱的設定名稱。</span><span class="sxs-lookup"><span data-stu-id="d19ce-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="d19ce-249">它會從秘密的名稱中去除版本前置詞，並傳回秘密名稱的其餘部分，以載入應用程式的設定名稱-值組。</span><span class="sxs-lookup"><span data-stu-id="d19ce-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="d19ce-250">當此方法實施時：</span><span class="sxs-lookup"><span data-stu-id="d19ce-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="d19ce-251">應用程式的專案檔中所指定的應用程式版本。</span><span class="sxs-lookup"><span data-stu-id="d19ce-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="d19ce-252">在下列範例中，應用程式的版本會設定為 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="d19ce-253">確認 `<UserSecretsId>` 屬性存在於應用程式的專案檔中，其中 `{GUID}` 是使用者提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="d19ce-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="d19ce-254">使用 [秘密管理員工具](xref:security/app-secrets)在本機儲存下列秘密：</span><span class="sxs-lookup"><span data-stu-id="d19ce-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="d19ce-255">您可以使用下列 Azure CLI 命令，將秘密儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="d19ce-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="d19ce-256">執行應用程式時，會載入金鑰保存庫秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="d19ce-257">的字串秘密 `5000-AppSecret` 會與應用程式專案檔中所指定的應用程式版本相符 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="d19ce-258">`5000`以虛線)  (的版本會從索引鍵名稱中移除。</span><span class="sxs-lookup"><span data-stu-id="d19ce-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="d19ce-259">在整個應用程式中，使用金鑰讀取設定會 `AppSecret` 載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="d19ce-260">如果將專案檔中的應用程式版本變更為 `5.1.0.0` ，並再次執行應用程式，則傳回的秘密值會 `5.1.0.0_secret_value_dev` 在開發環境和生產環境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="d19ce-261">您也可以提供自己的 <xref:Azure.Security.KeyVault.Secrets.SecretClient> 實作為瞭解 addazurekeyvault。</span><span class="sxs-lookup"><span data-stu-id="d19ce-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="d19ce-262">自訂用戶端允許跨應用程式共用用戶端的單一實例。</span><span class="sxs-lookup"><span data-stu-id="d19ce-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d19ce-263">將陣列繫結到類別</span><span class="sxs-lookup"><span data-stu-id="d19ce-263">Bind an array to a class</span></span>

<span data-ttu-id="d19ce-264">提供者能夠將設定值讀取至陣列，以系結至 POCO 陣列。</span><span class="sxs-lookup"><span data-stu-id="d19ce-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="d19ce-265">從允許金鑰包含冒號 () 分隔符號的設定來源進行讀取時 `:` ，會使用數值索引鍵區段來區分組成陣列的索引鍵 (`:0:` 、 `:1:` 、 &hellip; `:{n}:`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="d19ce-266">如需詳細資訊，請參閱設定：將陣列系結 [至類別](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="d19ce-267">Azure Key Vault 金鑰不能使用冒號做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="d19ce-268">本主題所述的方法會使用雙虛線 (`--`) 做為階層值的分隔符號 (區段) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="d19ce-269">陣列金鑰會儲存在 Azure Key Vault 中，並以雙連字號和數位鍵區段 (`--0--` 、 `--1--` &hellip; `--{n}--`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="d19ce-270">檢查 JSON 檔案所提供的下列 [Serilog](https://serilog.net/) 記錄提供者設定。</span><span class="sxs-lookup"><span data-stu-id="d19ce-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="d19ce-271">陣列中定義了兩個物件常值 `WriteTo` ，它會反映兩個 Serilog *接收*，其描述記錄輸出的目的地：</span><span class="sxs-lookup"><span data-stu-id="d19ce-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="d19ce-272">先前 JSON 檔案中顯示的設定會使用雙虛線 (`--`) 標記法和數值區段，儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="d19ce-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="d19ce-273">Key</span><span class="sxs-lookup"><span data-stu-id="d19ce-273">Key</span></span> | <span data-ttu-id="d19ce-274">值</span><span class="sxs-lookup"><span data-stu-id="d19ce-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="d19ce-275">重載秘密</span><span class="sxs-lookup"><span data-stu-id="d19ce-275">Reload secrets</span></span>

<span data-ttu-id="d19ce-276">在呼叫之前，會快取秘密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="d19ce-277">在執行之前，應用程式不會遵守金鑰保存庫中已過期、已停用和更新的秘密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="d19ce-278">已停用和過期的秘密</span><span class="sxs-lookup"><span data-stu-id="d19ce-278">Disabled and expired secrets</span></span>

<span data-ttu-id="d19ce-279">停用和過期的秘密會擲回 <xref:Azure.RequestFailedException> 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="d19ce-280">若要防止應用程式擲回，請使用不同的設定提供者來提供設定，或更新已停用或過期的密碼。</span><span class="sxs-lookup"><span data-stu-id="d19ce-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d19ce-281">疑難排解</span><span class="sxs-lookup"><span data-stu-id="d19ce-281">Troubleshoot</span></span>

<span data-ttu-id="d19ce-282">當應用程式無法使用提供者載入設定時，會將錯誤訊息寫入 [ASP.NET 核心記錄基礎結構](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d19ce-283">下列情況會導致無法載入設定：</span><span class="sxs-lookup"><span data-stu-id="d19ce-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="d19ce-284">Azure Active Directory 中的應用程式或憑證設定不正確。</span><span class="sxs-lookup"><span data-stu-id="d19ce-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="d19ce-285">金鑰保存庫不存在於 Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="d19ce-286">應用程式未獲授權存取金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="d19ce-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="d19ce-287">存取原則不包含 `Get` 和 `List` 許可權。</span><span class="sxs-lookup"><span data-stu-id="d19ce-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="d19ce-288">在金鑰保存庫中，設定資料 (的名稱/值組) 不正確地命名、遺失、停用或過期。</span><span class="sxs-lookup"><span data-stu-id="d19ce-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="d19ce-289">應用程式有錯誤的金鑰保存庫名稱 (`KeyVaultName`) 、AZURE Ad 應用程式識別碼 (`AzureADApplicationId`) 或 azure ad 憑證指紋 () `AzureADCertThumbprint` ，或 azure ad DirectoryId () `AzureADDirectoryId` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="d19ce-290">在您嘗試載入的值的應用程式中，設定機碼 (名稱) 不正確。</span><span class="sxs-lookup"><span data-stu-id="d19ce-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="d19ce-291">將應用程式的存取原則新增至金鑰保存庫時，已建立原則，但未在 [**存取原則**] UI 中選取 [**儲存**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d19ce-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d19ce-292">其他資源</span><span class="sxs-lookup"><span data-stu-id="d19ce-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="d19ce-293">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="d19ce-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="d19ce-294">Microsoft Azure： Key Vault 檔</span><span class="sxs-lookup"><span data-stu-id="d19ce-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="d19ce-295">如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰</span><span class="sxs-lookup"><span data-stu-id="d19ce-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="d19ce-296">KeyVaultClient 類別</span><span class="sxs-lookup"><span data-stu-id="d19ce-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="d19ce-297">快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密</span><span class="sxs-lookup"><span data-stu-id="d19ce-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="d19ce-298">教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Windows 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="d19ce-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d19ce-299">本檔說明如何使用 [Microsoft Azure Key vault](https://azure.microsoft.com/services/key-vault/) 設定提供者，從 Azure key vault 秘密載入應用程式設定值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="d19ce-300">Azure Key Vault 是一項雲端式服務，可協助保護應用程式和服務所使用的密碼編譯金鑰和秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="d19ce-301">搭配使用 Azure Key Vault 與 ASP.NET Core 應用程式的常見案例包括：</span><span class="sxs-lookup"><span data-stu-id="d19ce-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="d19ce-302">控制對敏感設定資料的存取。</span><span class="sxs-lookup"><span data-stu-id="d19ce-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="d19ce-303">在儲存設定資料時，符合 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 的需求。</span><span class="sxs-lookup"><span data-stu-id="d19ce-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="d19ce-304">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d19ce-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="d19ce-305">套件</span><span class="sxs-lookup"><span data-stu-id="d19ce-305">Packages</span></span>

<span data-ttu-id="d19ce-306">將封裝參考新增至 [Microsoft.Extensions.Configuration。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 封裝。</span><span class="sxs-lookup"><span data-stu-id="d19ce-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="d19ce-307">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="d19ce-307">Sample app</span></span>

<span data-ttu-id="d19ce-308">範例應用程式會以 Program.cs 檔頂端的語句所決定的兩種模式之一執行 `#define` ： </span><span class="sxs-lookup"><span data-stu-id="d19ce-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="d19ce-309">`Certificate`：示範如何使用 Azure 金鑰保存庫用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="d19ce-310">此版本的範例可從任何位置執行，其部署至 Azure App Service 或任何能夠提供 ASP.NET Core 應用程式的主機。</span><span class="sxs-lookup"><span data-stu-id="d19ce-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="d19ce-311">`Managed`：示範如何使用 azure [資源的受控](/azure/active-directory/managed-identities-azure-resources/overview) 識別，透過 azure AD 驗證來驗證 Azure Key Vault 中的應用程式，而不需將認證儲存在應用程式的程式碼或設定中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="d19ce-312">使用受控識別進行驗證時，不需要 Azure AD 應用程式識別碼和密碼 (用戶端密碼) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="d19ce-313">`Managed`範例的版本必須部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="d19ce-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="d19ce-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span><span class="sxs-lookup"><span data-stu-id="d19ce-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="d19ce-315">如需如何使用預處理器指示詞 () 設定範例應用程式的詳細資訊 `#define` ，請參閱 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="d19ce-316">開發環境中的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="d19ce-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="d19ce-317">使用 [秘密管理員工具](xref:security/app-secrets)在本機設定秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d19ce-318">當範例應用程式在開發環境中的本機電腦上執行時，會從本機使用者秘密存放區載入秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="d19ce-319">秘密管理員工具需要 `<UserSecretsId>` 應用程式專案檔案中的屬性。</span><span class="sxs-lookup"><span data-stu-id="d19ce-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="d19ce-320">將屬性值 (`{GUID}`) 設定為任何唯一的 GUID：</span><span class="sxs-lookup"><span data-stu-id="d19ce-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="d19ce-321">秘密會以名稱/值組的形式建立。</span><span class="sxs-lookup"><span data-stu-id="d19ce-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="d19ce-322">階層值 (設定區段) 使用 `:` (冒號) 作為 [ASP.NET 核心](xref:fundamentals/configuration/index) 設定機碼名稱中的分隔符號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="d19ce-323">秘密管理員是從開啟至專案 [內容根目錄](xref:fundamentals/index#content-root)的命令 shell 使用，其中 `{SECRET NAME}` 是名稱，而 `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="d19ce-324">從專案的 [內容根目錄](xref:fundamentals/index#content-root) 在命令 shell 中執行下列命令，以設定範例應用程式的密碼：</span><span class="sxs-lookup"><span data-stu-id="d19ce-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="d19ce-325">當這些秘密儲存在 Azure Key Vault 中， [並使用 Azure Key vault 區段在生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 時， `_dev` 尾碼會變更為 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="d19ce-326">尾碼會在應用程式的輸出中提供視覺提示，指出設定值的來源。</span><span class="sxs-lookup"><span data-stu-id="d19ce-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="d19ce-327">在生產環境中使用 Azure Key Vault 的秘密儲存體</span><span class="sxs-lookup"><span data-stu-id="d19ce-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="d19ce-328">[快速入門：使用 AZURE CLI 從 Azure Key Vault 設定及取出秘密](/azure/key-vault/quick-create-cli)的說明，在此摘要說明如何建立 azure 金鑰保存庫，並儲存範例應用程式所使用的秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="d19ce-329">如需進一步的詳細資料，請參閱主題。</span><span class="sxs-lookup"><span data-stu-id="d19ce-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="d19ce-330">在 [azure 入口網站](https://portal.azure.com/)中使用下列任何一種方法來開啟 azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="d19ce-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="d19ce-331">選取程式碼區塊右上角的 [試試看]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="d19ce-332">在文字方塊中使用搜尋字串「Azure CLI」。</span><span class="sxs-lookup"><span data-stu-id="d19ce-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="d19ce-333">使用 [ **啟動 Cloud shell** ] 按鈕，在瀏覽器中開啟 cloud shell。</span><span class="sxs-lookup"><span data-stu-id="d19ce-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="d19ce-334">選取 Azure 入口網站右上角功能表上的 **[Cloud Shell]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d19ce-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="d19ce-335">如需詳細資訊，請參閱 [AZURE CLI](/cli/azure/) 和 [azure Cloud Shell 的總覽](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="d19ce-336">如果您尚未經過驗證，請使用命令登入 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="d19ce-337">使用下列命令建立資源群組，其中 `{RESOURCE GROUP NAME}` 是新資源群組的資源組名，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d19ce-338">使用下列命令在資源群組中建立金鑰保存庫，其中 `{KEY VAULT NAME}` 是新金鑰保存庫的名稱，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d19ce-339">以名稱/值組的形式在金鑰保存庫中建立秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="d19ce-340">Azure Key Vault 秘密名稱僅限英數位元和連字號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="d19ce-341">階層值 (設定區段) 使用 `--` (兩個虛線) 作為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d19ce-342">金鑰保存庫秘密名稱中不允許使用冒號（通常用來分隔區段的 [ASP.NET 核心](xref:fundamentals/configuration/index)設定中的子機碼）。</span><span class="sxs-lookup"><span data-stu-id="d19ce-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="d19ce-343">因此，當秘密載入至應用程式的設定時，會使用兩個連字號，並將其交換為冒號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="d19ce-344">下列秘密適用于範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="d19ce-345">這些值包含後置詞 `_prod` ，可區別這些值與 `_dev` 開發環境中從使用者秘密載入的尾碼值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="d19ce-346">將取代 `{KEY VAULT NAME}` 為您在先前步驟中建立的金鑰保存庫名稱：</span><span class="sxs-lookup"><span data-stu-id="d19ce-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="d19ce-347">針對非 Azure 託管應用程式使用應用程式識別碼和 x.509 憑證</span><span class="sxs-lookup"><span data-stu-id="d19ce-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="d19ce-348">設定 Azure AD、Azure 金鑰保存庫和應用程式，以使用 Azure Active Directory 應用程式識別碼和 x.509 憑證來向金鑰保存庫進行驗證 **（當應用程式裝載于 Azure 之外**）。</span><span class="sxs-lookup"><span data-stu-id="d19ce-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="d19ce-349">如需詳細資訊，請參閱[關於金鑰、祕密和憑證](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="d19ce-350">雖然 Azure 中裝載的應用程式支援使用應用程式識別碼和 x.509 憑證，但我們建議在 azure 中裝載應用程式時，使用 [適用于 azure 資源的受控](#use-managed-identities-for-azure-resources) 識別。</span><span class="sxs-lookup"><span data-stu-id="d19ce-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="d19ce-351">受控識別不需要將憑證儲存在應用程式或開發環境中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="d19ce-352">當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用應用程式識別碼和 x.509 憑證 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="d19ce-353">建立 PKCS # 12 封存 (*.pfx*) 憑證。</span><span class="sxs-lookup"><span data-stu-id="d19ce-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="d19ce-354">建立憑證的選項包括 [Windows](/windows/desktop/seccrypto/makecert) 和 [OpenSSL](https://www.openssl.org/)上的 MakeCert。</span><span class="sxs-lookup"><span data-stu-id="d19ce-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="d19ce-355">將憑證安裝到目前使用者的個人憑證存儲中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="d19ce-356">將金鑰標示為可匯出是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="d19ce-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="d19ce-357">請注意憑證的指紋，此憑證稍後會在此程式中使用。</span><span class="sxs-lookup"><span data-stu-id="d19ce-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="d19ce-358">將 PKCS # 12 封存 (*.pfx*) 憑證匯出為 DER 編碼憑證 (*.cer*) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="d19ce-359">使用 Azure AD (**應用程式** 註冊) 註冊應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="d19ce-360">將 DER 編碼憑證 (*.cer*) 上傳至 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="d19ce-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="d19ce-361">在 Azure AD 中選取應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="d19ce-362">流覽至 **憑證 & 秘密**。</span><span class="sxs-lookup"><span data-stu-id="d19ce-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="d19ce-363">選取 [ **上傳憑證** ] 以上傳包含公開金鑰的憑證。</span><span class="sxs-lookup"><span data-stu-id="d19ce-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="d19ce-364">*.Cer*、 *pem* 或 *.crt* 憑證是可接受的。</span><span class="sxs-lookup"><span data-stu-id="d19ce-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="d19ce-365">將金鑰保存庫名稱、應用程式識別碼和憑證指紋儲存在應用程式的檔案中 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="d19ce-366">在 Azure 入口網站中流覽至 **金鑰保存庫** 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="d19ce-367">使用 Azure Key Vault 區段，選取您在 [生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中建立的金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="d19ce-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="d19ce-368">選取 [存取原則]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="d19ce-369">選取 [新增存取原則]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="d19ce-370">開啟 **秘密許可權** ，並提供具有 **取得** 和 **列出** 許可權的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="d19ce-371">選取 [ **選取主體** ]，然後依名稱選取已註冊的應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="d19ce-372">選取 [選取]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="d19ce-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="d19ce-373">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-373">Select **OK**.</span></span>
1. <span data-ttu-id="d19ce-374">選取 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="d19ce-374">Select **Save**.</span></span>
1. <span data-ttu-id="d19ce-375">部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="d19ce-375">Deploy the app.</span></span>

<span data-ttu-id="d19ce-376">`Certificate`範例應用程式會從 `IConfigurationRoot` 與秘密名稱相同的名稱取得其設定值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="d19ce-377">非階層式值：的值 `SecretName` 是使用取得 `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="d19ce-378">階層值 (區段) ：使用 `:` (冒號) 標記法或 `GetSection` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d19ce-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="d19ce-379">您可以使用下列其中一種方法來取得設定值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="d19ce-380">X.509 憑證由作業系統管理。</span><span class="sxs-lookup"><span data-stu-id="d19ce-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="d19ce-381">應用程式會 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用檔案所提供的值來呼叫 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="d19ce-382">範例值：</span><span class="sxs-lookup"><span data-stu-id="d19ce-382">Example values:</span></span>

* <span data-ttu-id="d19ce-383">金鑰保存庫名稱： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d19ce-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="d19ce-384">應用程式識別碼： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="d19ce-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="d19ce-385">憑證指紋： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="d19ce-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="d19ce-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d19ce-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="d19ce-387">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d19ce-388">在開發環境中，秘密值會以 `_dev` 尾碼載入。</span><span class="sxs-lookup"><span data-stu-id="d19ce-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="d19ce-389">在生產環境中，值會以後綴載入 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="d19ce-390">使用適用于 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="d19ce-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="d19ce-391">**部署至 azure 的應用程式** 可以利用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別，讓應用程式使用 azure AD 驗證來驗證 azure Key Vault，而不需要認證 (應用程式識別碼和密碼/用戶端密碼) 儲存在應用程式中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="d19ce-392">當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用適用于 Azure 資源的受控識別 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="d19ce-393">將保存庫名稱輸入應用程式的檔案 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="d19ce-394">範例應用程式在設定為版本時，不需要 (用戶端密碼) 的應用程式識別碼和密碼 `Managed` ，因此您可以忽略這些設定專案。</span><span class="sxs-lookup"><span data-stu-id="d19ce-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="d19ce-395">應用程式會部署至 Azure，而 Azure 會使用儲存在檔案中的保存庫名稱來驗證應用程式，以存取 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="d19ce-396">將範例應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="d19ce-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="d19ce-397">部署至 Azure App Service 的應用程式會在建立服務時自動向 Azure AD 註冊。</span><span class="sxs-lookup"><span data-stu-id="d19ce-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="d19ce-398">從部署中取得物件識別碼，以在下列命令中使用。</span><span class="sxs-lookup"><span data-stu-id="d19ce-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="d19ce-399">物件識別碼會顯示在 Azure 入口網站中 **Identity** App Service 的面板上。</span><span class="sxs-lookup"><span data-stu-id="d19ce-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="d19ce-400">使用 Azure CLI 和應用程式的物件識別碼，為應用程式提供 `list` `get` 存取金鑰保存庫的和許可權：</span><span class="sxs-lookup"><span data-stu-id="d19ce-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="d19ce-401">使用 Azure CLI、PowerShell 或 Azure 入口網站 **重新開機應用程式**。</span><span class="sxs-lookup"><span data-stu-id="d19ce-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="d19ce-402">範例應用程式：</span><span class="sxs-lookup"><span data-stu-id="d19ce-402">The sample app:</span></span>

* <span data-ttu-id="d19ce-403">建立 `AzureServiceTokenProvider` 沒有連接字串之類別的實例。</span><span class="sxs-lookup"><span data-stu-id="d19ce-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="d19ce-404">未提供連接字串時，提供者會嘗試從 Azure 資源的受控識別取得存取權杖。</span><span class="sxs-lookup"><span data-stu-id="d19ce-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="d19ce-405"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 實例權杖回呼建立新的。</span><span class="sxs-lookup"><span data-stu-id="d19ce-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="d19ce-406">此 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 實例會與的預設執行一起使用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，以載入所有的秘密值，並以冒號取代雙虛線 (`--`)  (`:`) 的索引鍵名稱。</span><span class="sxs-lookup"><span data-stu-id="d19ce-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="d19ce-407">金鑰保存庫名稱範例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d19ce-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="d19ce-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d19ce-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="d19ce-409">當您執行應用程式時，網頁會顯示已載入的秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d19ce-410">在開發環境中，秘密值具有 `_dev` 尾碼，因為它們是由使用者密碼提供。</span><span class="sxs-lookup"><span data-stu-id="d19ce-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="d19ce-411">在生產環境中，值會以後綴載入， `_prod` 因為它們是由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="d19ce-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="d19ce-412">如果您收到 `Access denied` 錯誤，請確認應用程式已向 AZURE AD 註冊，並已提供金鑰保存庫的存取權。</span><span class="sxs-lookup"><span data-stu-id="d19ce-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="d19ce-413">確認您已在 Azure 中重新開機服務。</span><span class="sxs-lookup"><span data-stu-id="d19ce-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="d19ce-414">如需搭配受控識別和 Azure DevOps 管線使用提供者的詳細資訊，請參閱使用 [受控服務識別建立與 VM 的 Azure Resource Manager 服務](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)連線。</span><span class="sxs-lookup"><span data-stu-id="d19ce-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="d19ce-415">使用金鑰名稱前置詞</span><span class="sxs-lookup"><span data-stu-id="d19ce-415">Use a key name prefix</span></span>

<span data-ttu-id="d19ce-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 提供接受執行的多載，可 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 讓您控制如何將金鑰保存庫密碼轉換成設定金鑰。</span><span class="sxs-lookup"><span data-stu-id="d19ce-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="d19ce-417">例如，您可以根據您在應用程式啟動時所提供的前置詞值，執行介面以載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="d19ce-418">例如，這可讓您根據應用程式的版本載入秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="d19ce-419">請勿在金鑰保存庫秘密上使用前置詞，將多個應用程式的秘密放入相同的金鑰保存庫中，或將環境秘密 (例如， *開發* 和 *生產* 秘密) 至相同的保存庫。</span><span class="sxs-lookup"><span data-stu-id="d19ce-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="d19ce-420">建議不同的應用程式和開發/生產環境使用不同的金鑰保存庫，以隔離最高層級安全性的應用程式環境。</span><span class="sxs-lookup"><span data-stu-id="d19ce-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="d19ce-421">下列範例會在金鑰保存庫中建立秘密 (並且針對開發環境使用 Secret Manager 工具) `5000-AppSecret` 不允許在金鑰保存庫秘密名稱) 中使用 (期間。</span><span class="sxs-lookup"><span data-stu-id="d19ce-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="d19ce-422">此秘密代表應用程式版本5.0.0.0 的應用程式密碼。</span><span class="sxs-lookup"><span data-stu-id="d19ce-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="d19ce-423">針對另一個版本的應用程式，5.1.0.0 會將秘密新增至金鑰保存庫 (，並使用) 的「秘密管理員」工具 `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="d19ce-424">每個應用程式版本都會將其已建立版本的秘密值載入至其設定中，並在 `AppSecret` 載入密碼時將其移除。</span><span class="sxs-lookup"><span data-stu-id="d19ce-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="d19ce-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用自訂來呼叫 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="d19ce-426">此 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 執行會回應秘密的版本前置詞，以將適當的秘密載入設定：</span><span class="sxs-lookup"><span data-stu-id="d19ce-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="d19ce-427">`Load` 在名稱開頭為前置詞時載入秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="d19ce-428">未載入其他秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="d19ce-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="d19ce-429">`GetKey`:</span></span>
  * <span data-ttu-id="d19ce-430">移除秘密名稱中的前置詞。</span><span class="sxs-lookup"><span data-stu-id="d19ce-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="d19ce-431">使用的任何名稱取代兩個虛線 `KeyDelimiter` ，也就是設定 (中使用的分隔符號，通常是) 的冒號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="d19ce-432">Azure Key Vault 不允許秘密名稱中有冒號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="d19ce-433">`Load`方法是由提供者演算法呼叫，此演算法會逐一查看保存庫秘密，以找出具有版本首碼的金鑰。</span><span class="sxs-lookup"><span data-stu-id="d19ce-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="d19ce-434">當找到版本首碼時 `Load` ，演算法會使用 `GetKey` 方法來傳回秘密名稱的設定名稱。</span><span class="sxs-lookup"><span data-stu-id="d19ce-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="d19ce-435">它會從秘密的名稱中去除版本前置詞，並傳回秘密名稱的其餘部分，以載入應用程式的設定名稱-值組。</span><span class="sxs-lookup"><span data-stu-id="d19ce-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="d19ce-436">當此方法實施時：</span><span class="sxs-lookup"><span data-stu-id="d19ce-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="d19ce-437">應用程式的專案檔中所指定的應用程式版本。</span><span class="sxs-lookup"><span data-stu-id="d19ce-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="d19ce-438">在下列範例中，應用程式的版本會設定為 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="d19ce-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="d19ce-439">確認 `<UserSecretsId>` 屬性存在於應用程式的專案檔中，其中 `{GUID}` 是使用者提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="d19ce-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="d19ce-440">使用 [秘密管理員工具](xref:security/app-secrets)在本機儲存下列秘密：</span><span class="sxs-lookup"><span data-stu-id="d19ce-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="d19ce-441">您可以使用下列 Azure CLI 命令，將秘密儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="d19ce-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="d19ce-442">執行應用程式時，會載入金鑰保存庫秘密。</span><span class="sxs-lookup"><span data-stu-id="d19ce-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="d19ce-443">的字串秘密 `5000-AppSecret` 會與應用程式專案檔中所指定的應用程式版本相符 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="d19ce-444">`5000`以虛線)  (的版本會從索引鍵名稱中移除。</span><span class="sxs-lookup"><span data-stu-id="d19ce-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="d19ce-445">在整個應用程式中，使用金鑰讀取設定會 `AppSecret` 載入秘密值。</span><span class="sxs-lookup"><span data-stu-id="d19ce-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="d19ce-446">如果將專案檔中的應用程式版本變更為 `5.1.0.0` ，並再次執行應用程式，則傳回的秘密值會 `5.1.0.0_secret_value_dev` 在開發環境和生產環境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="d19ce-447">您也可以提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 實作為 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="d19ce-448">自訂用戶端允許跨應用程式共用用戶端的單一實例。</span><span class="sxs-lookup"><span data-stu-id="d19ce-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d19ce-449">將陣列繫結到類別</span><span class="sxs-lookup"><span data-stu-id="d19ce-449">Bind an array to a class</span></span>

<span data-ttu-id="d19ce-450">提供者能夠將設定值讀取至陣列，以系結至 POCO 陣列。</span><span class="sxs-lookup"><span data-stu-id="d19ce-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="d19ce-451">從允許金鑰包含冒號 () 分隔符號的設定來源進行讀取時 `:` ，會使用數值索引鍵區段來區分組成陣列的索引鍵 (`:0:` 、 `:1:` 、 &hellip; `:{n}:`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="d19ce-452">如需詳細資訊，請參閱設定：將陣列系結 [至類別](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="d19ce-453">Azure Key Vault 金鑰不能使用冒號做為分隔符號。</span><span class="sxs-lookup"><span data-stu-id="d19ce-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="d19ce-454">本主題所述的方法會使用雙虛線 (`--`) 做為階層值的分隔符號 (區段) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="d19ce-455">陣列金鑰會儲存在 Azure Key Vault 中，並以雙連字號和數位鍵區段 (`--0--` 、 `--1--` &hellip; `--{n}--`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="d19ce-456">檢查 JSON 檔案所提供的下列 [Serilog](https://serilog.net/) 記錄提供者設定。</span><span class="sxs-lookup"><span data-stu-id="d19ce-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="d19ce-457">陣列中定義了兩個物件常值 `WriteTo` ，它會反映兩個 Serilog *接收*，其描述記錄輸出的目的地：</span><span class="sxs-lookup"><span data-stu-id="d19ce-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="d19ce-458">先前 JSON 檔案中顯示的設定會使用雙虛線 (`--`) 標記法和數值區段，儲存在 Azure Key Vault 中：</span><span class="sxs-lookup"><span data-stu-id="d19ce-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="d19ce-459">Key</span><span class="sxs-lookup"><span data-stu-id="d19ce-459">Key</span></span> | <span data-ttu-id="d19ce-460">值</span><span class="sxs-lookup"><span data-stu-id="d19ce-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="d19ce-461">重載秘密</span><span class="sxs-lookup"><span data-stu-id="d19ce-461">Reload secrets</span></span>

<span data-ttu-id="d19ce-462">在呼叫之前，會快取秘密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="d19ce-463">在執行之前，應用程式不會遵守金鑰保存庫中已過期、已停用和更新的秘密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="d19ce-464">已停用和過期的秘密</span><span class="sxs-lookup"><span data-stu-id="d19ce-464">Disabled and expired secrets</span></span>

<span data-ttu-id="d19ce-465">停用和過期的秘密會擲回 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="d19ce-466">若要防止應用程式擲回，請使用不同的設定提供者來提供設定，或更新已停用或過期的密碼。</span><span class="sxs-lookup"><span data-stu-id="d19ce-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d19ce-467">疑難排解</span><span class="sxs-lookup"><span data-stu-id="d19ce-467">Troubleshoot</span></span>

<span data-ttu-id="d19ce-468">當應用程式無法使用提供者載入設定時，會將錯誤訊息寫入 [ASP.NET 核心記錄基礎結構](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="d19ce-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d19ce-469">下列情況會導致無法載入設定：</span><span class="sxs-lookup"><span data-stu-id="d19ce-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="d19ce-470">Azure Active Directory 中的應用程式或憑證設定不正確。</span><span class="sxs-lookup"><span data-stu-id="d19ce-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="d19ce-471">金鑰保存庫不存在於 Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="d19ce-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="d19ce-472">應用程式未獲授權存取金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="d19ce-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="d19ce-473">存取原則不包含 `Get` 和 `List` 許可權。</span><span class="sxs-lookup"><span data-stu-id="d19ce-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="d19ce-474">在金鑰保存庫中，設定資料 (的名稱/值組) 不正確地命名、遺失、停用或過期。</span><span class="sxs-lookup"><span data-stu-id="d19ce-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="d19ce-475">應用程式有錯誤的金鑰保存庫名稱 (`KeyVaultName`) 、AZURE Ad 應用程式識別碼 (`AzureADApplicationId`) 或 azure ad 憑證指紋 (`AzureADCertThumbprint`) 。</span><span class="sxs-lookup"><span data-stu-id="d19ce-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="d19ce-476">在您嘗試載入的值的應用程式中，設定機碼 (名稱) 不正確。</span><span class="sxs-lookup"><span data-stu-id="d19ce-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="d19ce-477">將應用程式的存取原則新增至金鑰保存庫時，已建立原則，但未在 [**存取原則**] UI 中選取 [**儲存**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d19ce-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d19ce-478">其他資源</span><span class="sxs-lookup"><span data-stu-id="d19ce-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="d19ce-479">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="d19ce-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="d19ce-480">Microsoft Azure： Key Vault 檔</span><span class="sxs-lookup"><span data-stu-id="d19ce-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="d19ce-481">如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰</span><span class="sxs-lookup"><span data-stu-id="d19ce-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="d19ce-482">KeyVaultClient 類別</span><span class="sxs-lookup"><span data-stu-id="d19ce-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="d19ce-483">快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密</span><span class="sxs-lookup"><span data-stu-id="d19ce-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="d19ce-484">教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Windows 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="d19ce-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
