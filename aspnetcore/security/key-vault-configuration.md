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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core 中的 Azure Key Vault 設定提供者

[Andrew Stanton-護士](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

本檔說明如何使用 [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/) 設定提供者，從 Azure key vault 秘密載入應用程式設定值。 Azure Key Vault 是一項雲端式服務，可協助保護應用程式和服務所使用的密碼編譯金鑰和秘密。 搭配使用 Azure Key Vault 與 ASP.NET Core 應用程式的常見案例包括：

* 控制對敏感設定資料的存取。
* 在儲存設定資料時，符合 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 的需求。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="packages"></a>套件

新增下列封裝的套件參考：

* [Azure.Extensions.AspNetCore.Configuration。秘密](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* [蔚藍。Identity](https://www.nuget.org/packages/Azure.Identity)

## <a name="sample-app"></a>範例應用程式

範例應用程式會以 Program.cs 檔頂端的語句所決定的兩種模式之一執行 `#define` ： 

* `Certificate`：示範如何使用 Azure 金鑰保存庫用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。 此版本的範例可從任何位置執行，其部署至 Azure App Service 或任何能夠提供 ASP.NET Core 應用程式的主機。
* `Managed`：示範如何使用 azure [資源的受控](/azure/active-directory/managed-identities-azure-resources/overview) 識別，透過 azure AD 驗證來驗證 Azure Key Vault 中的應用程式，而不需將認證儲存在應用程式的程式碼或設定中。 使用受控識別進行驗證時，不需要 Azure AD 應用程式識別碼和密碼 (用戶端密碼) 。 `Managed`範例的版本必須部署至 Azure。 Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.

如需如何使用預處理器指示詞 () 設定範例應用程式的詳細資訊 `#define` ，請參閱 <xref:index#preprocessor-directives-in-sample-code> 。

## <a name="secret-storage-in-the-development-environment"></a>開發環境中的秘密儲存體

使用 [秘密管理員工具](xref:security/app-secrets)在本機設定秘密。 當範例應用程式在開發環境中的本機電腦上執行時，會從本機使用者秘密存放區載入秘密。

秘密管理員工具需要 `<UserSecretsId>` 應用程式專案檔案中的屬性。 將屬性值 (`{GUID}`) 設定為任何唯一的 GUID：

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

秘密會以名稱/值組的形式建立。 階層值 (設定區段) 使用 `:` (冒號) 作為 [ASP.NET 核心](xref:fundamentals/configuration/index) 設定機碼名稱中的分隔符號。

秘密管理員是從開啟至專案 [內容根目錄](xref:fundamentals/index#content-root)的命令 shell 使用，其中 `{SECRET NAME}` 是名稱，而 `{SECRET VALUE}` 是值：

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

從專案的 [內容根目錄](xref:fundamentals/index#content-root) 在命令 shell 中執行下列命令，以設定範例應用程式的密碼：

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

當這些秘密儲存在 Azure Key Vault 中， [並使用 Azure Key vault 區段在生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 時， `_dev` 尾碼會變更為 `_prod` 。 尾碼會在應用程式的輸出中提供視覺提示，指出設定值的來源。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>在生產環境中使用 Azure Key Vault 的秘密儲存體

[快速入門：使用 AZURE CLI 從 Azure Key Vault 設定及取出秘密](/azure/key-vault/quick-create-cli)的說明，在此摘要說明如何建立 azure 金鑰保存庫，並儲存範例應用程式所使用的秘密。 如需進一步的詳細資料，請參閱主題。

1. 在 [azure 入口網站](https://portal.azure.com/)中使用下列任何一種方法來開啟 azure Cloud shell：

   * 選取程式碼區塊右上角的 [試試看]。 在文字方塊中使用搜尋字串「Azure CLI」。
   * 使用 [ **啟動 Cloud shell** ] 按鈕，在瀏覽器中開啟 cloud shell。
   * 選取 Azure 入口網站右上角功能表上的 **[Cloud Shell]** 按鈕。

   如需詳細資訊，請參閱 [AZURE CLI](/cli/azure/) 和 [azure Cloud Shell 的總覽](/azure/cloud-shell/overview)。

1. 如果您尚未經過驗證，請使用命令登入 `az login` 。

1. 使用下列命令建立資源群組，其中 `{RESOURCE GROUP NAME}` 是新資源群組的資源組名，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 使用下列命令在資源群組中建立金鑰保存庫，其中 `{KEY VAULT NAME}` 是新金鑰保存庫的名稱，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 以名稱/值組的形式在金鑰保存庫中建立秘密。

   Azure Key Vault 秘密名稱僅限英數位元和連字號。 階層值 (設定區段) 使用 `--` (兩個虛線) 作為分隔符號。 金鑰保存庫秘密名稱中不允許使用冒號（通常用來分隔區段的 [ASP.NET 核心](xref:fundamentals/configuration/index)設定中的子機碼）。 因此，當秘密載入至應用程式的設定時，會使用兩個連字號，並將其交換為冒號。

   下列秘密適用于範例應用程式。 這些值包含後置詞 `_prod` ，可區別這些值與 `_dev` 開發環境中從使用者秘密載入的尾碼值。 將取代 `{KEY VAULT NAME}` 為您在先前步驟中建立的金鑰保存庫名稱：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>針對非 Azure 託管應用程式使用應用程式識別碼和 x.509 憑證

設定 Azure AD、Azure 金鑰保存庫和應用程式，以使用 Azure Active Directory 應用程式識別碼和 x.509 憑證來向金鑰保存庫進行驗證 **（當應用程式裝載于 Azure 之外**）。 如需詳細資訊，請參閱[關於金鑰、祕密和憑證](/azure/key-vault/about-keys-secrets-and-certificates)。

> [!NOTE]
> 雖然 Azure 中裝載的應用程式支援使用應用程式識別碼和 x.509 憑證，但我們建議在 azure 中裝載應用程式時，使用 [適用于 azure 資源的受控](#use-managed-identities-for-azure-resources) 識別。 受控識別不需要將憑證儲存在應用程式或開發環境中。

當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用應用程式識別碼和 x.509 憑證 `Certificate` 。

1. 建立 PKCS # 12 封存 (*.pfx*) 憑證。 建立憑證的選項包括 [Windows](/windows/desktop/seccrypto/makecert) 和 [OpenSSL](https://www.openssl.org/)上的 MakeCert。
1. 將憑證安裝到目前使用者的個人憑證存儲中。 將金鑰標示為可匯出是選擇性的。 請注意憑證的指紋，此憑證稍後會在此程式中使用。
1. 將 PKCS # 12 封存 (*.pfx*) 憑證匯出為 DER 編碼憑證 (*.cer*) 。
1. 使用 Azure AD (**應用程式** 註冊) 註冊應用程式。
1. 將 DER 編碼憑證 (*.cer*) 上傳至 Azure AD：
   1. 在 Azure AD 中選取應用程式。
   1. 流覽至 **憑證 & 秘密**。
   1. 選取 [ **上傳憑證** ] 以上傳包含公開金鑰的憑證。 *.Cer*、 *pem* 或 *.crt* 憑證是可接受的。
1. 將金鑰保存庫名稱、應用程式識別碼和憑證指紋儲存在應用程式的檔案中 *appsettings.json* 。
1. 在 Azure 入口網站中流覽至 **金鑰保存庫** 。
1. 使用 Azure Key Vault 區段，選取您在 [生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中建立的金鑰保存庫。
1. 選取 [存取原則]。
1. 選取 [新增存取原則]。
1. 開啟 **秘密許可權** ，並提供具有 **取得** 和 **列出** 許可權的應用程式。
1. 選取 [ **選取主體** ]，然後依名稱選取已註冊的應用程式。 選取 [選取]  按鈕。
1. 選取 [確定]。
1. 選取 [儲存]。
1. 部署應用程式。

`Certificate`範例應用程式會從 `IConfigurationRoot` 與秘密名稱相同的名稱取得其設定值：

* 非階層式值：的值 `SecretName` 是使用取得 `config["SecretName"]` 。
* 階層值 (區段) ：使用 `:` (冒號) 標記法或 `GetSection` 擴充方法。 您可以使用下列其中一種方法來取得設定值：
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 憑證由作業系統管理。 應用程式會使用檔案所提供的值來呼叫 **瞭解 addazurekeyvault** *appsettings.json* ：

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

範例值：

* 金鑰保存庫名稱： `contosovault`
* 應用程式識別碼： `627e911e-43cc-61d4-992e-12db9c81b413`
* 憑證指紋： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

當您執行應用程式時，網頁會顯示已載入的秘密值。 在開發環境中，秘密值會以 `_dev` 尾碼載入。 在生產環境中，值會以後綴載入 `_prod` 。

## <a name="use-managed-identities-for-azure-resources"></a>使用適用于 Azure 資源的受控識別

**部署至 azure 的應用程式** 可以利用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別，讓應用程式使用 azure AD 驗證來驗證 azure Key Vault，而不需要認證 (應用程式識別碼和密碼/用戶端密碼) 儲存在應用程式中。

當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用適用于 Azure 資源的受控識別 `Managed` 。

將保存庫名稱輸入應用程式的檔案 *appsettings.json* 。 範例應用程式在設定為版本時，不需要 (用戶端密碼) 的應用程式識別碼和密碼 `Managed` ，因此您可以忽略這些設定專案。 應用程式會部署至 Azure，而 Azure 會使用儲存在檔案中的保存庫名稱來驗證應用程式，以存取 Azure Key Vault *appsettings.json* 。

將範例應用程式部署至 Azure App Service。

部署至 Azure App Service 的應用程式會在建立服務時自動向 Azure AD 註冊。 從部署中取得物件識別碼，以在下列命令中使用。 物件識別碼會顯示在 Azure 入口網站中 **Identity** App Service 的面板上。

使用 Azure CLI 和應用程式的物件識別碼，為應用程式提供 `list` `get` 存取金鑰保存庫的和許可權：

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

使用 Azure CLI、PowerShell 或 Azure 入口網站 **重新開機應用程式**。

範例應用程式：

* 建立類別的實例 `DefaultAzureCredential` ，認證會嘗試從 Azure 資源的環境取得存取權杖。
* [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets)使用實例建立新的 `DefaultAzureCredential` 。
* 此 `Azure.Security.KeyVault.Secrets.Secrets` 實例會與的預設執行一起使用 `Azure.Extensions.AspNetCore.Configuration.Secrets` ，以載入所有的秘密值，並以冒號取代雙虛線 (`--`)  (`:`) 的索引鍵名稱。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

金鑰保存庫名稱範例值： `contosovault`

*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

當您執行應用程式時，網頁會顯示已載入的秘密值。 在開發環境中，秘密值具有 `_dev` 尾碼，因為它們是由使用者密碼提供。 在生產環境中，值會以後綴載入， `_prod` 因為它們是由 Azure Key Vault 提供。

如果您收到 `Access denied` 錯誤，請確認應用程式已向 AZURE AD 註冊，並已提供金鑰保存庫的存取權。 確認您已在 Azure 中重新開機服務。

如需搭配受控識別和 Azure DevOps 管線使用提供者的詳細資訊，請參閱使用 [受控服務識別建立與 VM 的 Azure Resource Manager 服務](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)連線。

## <a name="configuration-options"></a>設定選項

瞭解 addazurekeyvault 可以接受 AzureKeyVaultConfigurationOptions：

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| 屬性         | 描述 |
| ---------------- | ----------- |
| `Manager`        | `Azure.Extensions.AspNetCore.Configuration.Secrets` 用來控制秘密載入的實例。 |
| `ReloadInterval` | `Timespan` 在輪詢金鑰保存庫以進行變更的嘗試之間等候。 預設值為 `null` (設定) 不會重載。 |

## <a name="use-a-key-name-prefix"></a>使用金鑰名稱前置詞

瞭解 addazurekeyvault 會提供接受執行的多載 `Azure.Extensions.AspNetCore.Configuration.Secrets` ，可讓您控制如何將金鑰保存庫密碼轉換成設定金鑰。 例如，您可以根據您在應用程式啟動時所提供的前置詞值，執行介面以載入秘密值。 例如，這可讓您根據應用程式的版本載入秘密。

> [!WARNING]
> 請勿在金鑰保存庫秘密上使用前置詞，將多個應用程式的秘密放入相同的金鑰保存庫中，或將環境秘密 (例如， *開發* 和 *生產* 秘密) 至相同的保存庫。 建議不同的應用程式和開發/生產環境使用不同的金鑰保存庫，以隔離最高層級安全性的應用程式環境。

下列範例會在金鑰保存庫中建立秘密 (並且針對開發環境使用 Secret Manager 工具) `5000-AppSecret` 不允許在金鑰保存庫秘密名稱) 中使用 (期間。 此秘密代表應用程式版本5.0.0.0 的應用程式密碼。 針對另一個版本的應用程式，5.1.0.0 會將秘密新增至金鑰保存庫 (，並使用) 的「秘密管理員」工具 `5100-AppSecret` 。 每個應用程式版本都會將其已建立版本的秘密值載入至其設定中，並在 `AppSecret` 載入密碼時將其移除。

使用自訂來呼叫瞭解 addazurekeyvault `Azure.Extensions.AspNetCore.Configuration.Secrets` ：

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

此 `Azure.Extensions.AspNetCore.Configuration.Secrets` 執行會回應秘密的版本前置詞，以將適當的秘密載入設定：

* `Load` 在名稱開頭為前置詞時載入秘密。 未載入其他秘密。
* `GetKey`:
  * 移除秘密名稱中的前置詞。
  * 使用的任何名稱取代兩個虛線 `KeyDelimiter` ，也就是設定 (中使用的分隔符號，通常是) 的冒號。 Azure Key Vault 不允許秘密名稱中有冒號。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`方法是由提供者演算法呼叫，此演算法會逐一查看保存庫秘密，以找出具有版本首碼的金鑰。 當找到版本首碼時 `Load` ，演算法會使用 `GetKey` 方法來傳回秘密名稱的設定名稱。 它會從秘密的名稱中去除版本前置詞，並傳回秘密名稱的其餘部分，以載入應用程式的設定名稱-值組。

當此方法實施時：

1. 應用程式的專案檔中所指定的應用程式版本。 在下列範例中，應用程式的版本會設定為 `5.0.0.0` ：

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. 確認 `<UserSecretsId>` 屬性存在於應用程式的專案檔中，其中 `{GUID}` 是使用者提供的 GUID：

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   使用 [秘密管理員工具](xref:security/app-secrets)在本機儲存下列秘密：

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 您可以使用下列 Azure CLI 命令，將秘密儲存在 Azure Key Vault 中：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 執行應用程式時，會載入金鑰保存庫秘密。 的字串秘密 `5000-AppSecret` 會與應用程式專案檔中所指定的應用程式版本相符 (`5.0.0.0`) 。

1. `5000`以虛線)  (的版本會從索引鍵名稱中移除。 在整個應用程式中，使用金鑰讀取設定會 `AppSecret` 載入秘密值。

1. 如果將專案檔中的應用程式版本變更為 `5.1.0.0` ，並再次執行應用程式，則傳回的秘密值會 `5.1.0.0_secret_value_dev` 在開發環境和生產環境中 `5.1.0.0_secret_value_prod` 。

> [!NOTE]
> 您也可以提供自己的 <xref:Azure.Security.KeyVault.Secrets.SecretClient> 實作為瞭解 addazurekeyvault。 自訂用戶端允許跨應用程式共用用戶端的單一實例。

## <a name="bind-an-array-to-a-class"></a>將陣列繫結到類別

提供者能夠將設定值讀取至陣列，以系結至 POCO 陣列。

從允許金鑰包含冒號 () 分隔符號的設定來源進行讀取時 `:` ，會使用數值索引鍵區段來區分組成陣列的索引鍵 (`:0:` 、 `:1:` 、 &hellip; `:{n}:`) 。 如需詳細資訊，請參閱設定：將陣列系結 [至類別](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。

Azure Key Vault 金鑰不能使用冒號做為分隔符號。 本主題所述的方法會使用雙虛線 (`--`) 做為階層值的分隔符號 (區段) 。 陣列金鑰會儲存在 Azure Key Vault 中，並以雙連字號和數位鍵區段 (`--0--` 、 `--1--` &hellip; `--{n}--`) 。

檢查 JSON 檔案所提供的下列 [Serilog](https://serilog.net/) 記錄提供者設定。 陣列中定義了兩個物件常值 `WriteTo` ，它會反映兩個 Serilog *接收*，其描述記錄輸出的目的地：

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

先前 JSON 檔案中顯示的設定會使用雙虛線 (`--`) 標記法和數值區段，儲存在 Azure Key Vault 中：

| Key | 值 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>重載秘密

在呼叫之前，會快取秘密 `IConfigurationRoot.Reload()` 。 在執行之前，應用程式不會遵守金鑰保存庫中已過期、已停用和更新的秘密 `Reload` 。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>已停用和過期的秘密

停用和過期的秘密會擲回 <xref:Azure.RequestFailedException> 。 若要防止應用程式擲回，請使用不同的設定提供者來提供設定，或更新已停用或過期的密碼。

## <a name="troubleshoot"></a>疑難排解

當應用程式無法使用提供者載入設定時，會將錯誤訊息寫入 [ASP.NET 核心記錄基礎結構](xref:fundamentals/logging/index)。 下列情況會導致無法載入設定：

* Azure Active Directory 中的應用程式或憑證設定不正確。
* 金鑰保存庫不存在於 Azure Key Vault 中。
* 應用程式未獲授權存取金鑰保存庫。
* 存取原則不包含 `Get` 和 `List` 許可權。
* 在金鑰保存庫中，設定資料 (的名稱/值組) 不正確地命名、遺失、停用或過期。
* 應用程式有錯誤的金鑰保存庫名稱 (`KeyVaultName`) 、AZURE Ad 應用程式識別碼 (`AzureADApplicationId`) 或 azure ad 憑證指紋 () `AzureADCertThumbprint` ，或 azure ad DirectoryId () `AzureADDirectoryId` 。
* 在您嘗試載入的值的應用程式中，設定機碼 (名稱) 不正確。
* 將應用程式的存取原則新增至金鑰保存庫時，已建立原則，但未在 [**存取原則**] UI 中選取 [**儲存**] 按鈕。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/configuration/index>
* [Microsoft Azure： Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure： Key Vault 檔](/azure/key-vault/)
* [如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 類別](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密](/azure/key-vault/quick-create-net)
* [教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Windows 虛擬機器](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本檔說明如何使用 [Microsoft Azure Key vault](https://azure.microsoft.com/services/key-vault/) 設定提供者，從 Azure key vault 秘密載入應用程式設定值。 Azure Key Vault 是一項雲端式服務，可協助保護應用程式和服務所使用的密碼編譯金鑰和秘密。 搭配使用 Azure Key Vault 與 ASP.NET Core 應用程式的常見案例包括：

* 控制對敏感設定資料的存取。
* 在儲存設定資料時，符合 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 的需求。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="packages"></a>套件

將封裝參考新增至 [Microsoft.Extensions.Configuration。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 封裝。

## <a name="sample-app"></a>範例應用程式

範例應用程式會以 Program.cs 檔頂端的語句所決定的兩種模式之一執行 `#define` ： 

* `Certificate`：示範如何使用 Azure 金鑰保存庫用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。 此版本的範例可從任何位置執行，其部署至 Azure App Service 或任何能夠提供 ASP.NET Core 應用程式的主機。
* `Managed`：示範如何使用 azure [資源的受控](/azure/active-directory/managed-identities-azure-resources/overview) 識別，透過 azure AD 驗證來驗證 Azure Key Vault 中的應用程式，而不需將認證儲存在應用程式的程式碼或設定中。 使用受控識別進行驗證時，不需要 Azure AD 應用程式識別碼和密碼 (用戶端密碼) 。 `Managed`範例的版本必須部署至 Azure。 Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.

如需如何使用預處理器指示詞 () 設定範例應用程式的詳細資訊 `#define` ，請參閱 <xref:index#preprocessor-directives-in-sample-code> 。

## <a name="secret-storage-in-the-development-environment"></a>開發環境中的秘密儲存體

使用 [秘密管理員工具](xref:security/app-secrets)在本機設定秘密。 當範例應用程式在開發環境中的本機電腦上執行時，會從本機使用者秘密存放區載入秘密。

秘密管理員工具需要 `<UserSecretsId>` 應用程式專案檔案中的屬性。 將屬性值 (`{GUID}`) 設定為任何唯一的 GUID：

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

秘密會以名稱/值組的形式建立。 階層值 (設定區段) 使用 `:` (冒號) 作為 [ASP.NET 核心](xref:fundamentals/configuration/index) 設定機碼名稱中的分隔符號。

秘密管理員是從開啟至專案 [內容根目錄](xref:fundamentals/index#content-root)的命令 shell 使用，其中 `{SECRET NAME}` 是名稱，而 `{SECRET VALUE}` 是值：

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

從專案的 [內容根目錄](xref:fundamentals/index#content-root) 在命令 shell 中執行下列命令，以設定範例應用程式的密碼：

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

當這些秘密儲存在 Azure Key Vault 中， [並使用 Azure Key vault 區段在生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 時， `_dev` 尾碼會變更為 `_prod` 。 尾碼會在應用程式的輸出中提供視覺提示，指出設定值的來源。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>在生產環境中使用 Azure Key Vault 的秘密儲存體

[快速入門：使用 AZURE CLI 從 Azure Key Vault 設定及取出秘密](/azure/key-vault/quick-create-cli)的說明，在此摘要說明如何建立 azure 金鑰保存庫，並儲存範例應用程式所使用的秘密。 如需進一步的詳細資料，請參閱主題。

1. 在 [azure 入口網站](https://portal.azure.com/)中使用下列任何一種方法來開啟 azure Cloud shell：

   * 選取程式碼區塊右上角的 [試試看]。 在文字方塊中使用搜尋字串「Azure CLI」。
   * 使用 [ **啟動 Cloud shell** ] 按鈕，在瀏覽器中開啟 cloud shell。
   * 選取 Azure 入口網站右上角功能表上的 **[Cloud Shell]** 按鈕。

   如需詳細資訊，請參閱 [AZURE CLI](/cli/azure/) 和 [azure Cloud Shell 的總覽](/azure/cloud-shell/overview)。

1. 如果您尚未經過驗證，請使用命令登入 `az login` 。

1. 使用下列命令建立資源群組，其中 `{RESOURCE GROUP NAME}` 是新資源群組的資源組名，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 使用下列命令在資源群組中建立金鑰保存庫，其中 `{KEY VAULT NAME}` 是新金鑰保存庫的名稱，而 `{LOCATION}` 是 Azure 區域 (datacenter) ：

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 以名稱/值組的形式在金鑰保存庫中建立秘密。

   Azure Key Vault 秘密名稱僅限英數位元和連字號。 階層值 (設定區段) 使用 `--` (兩個虛線) 作為分隔符號。 金鑰保存庫秘密名稱中不允許使用冒號（通常用來分隔區段的 [ASP.NET 核心](xref:fundamentals/configuration/index)設定中的子機碼）。 因此，當秘密載入至應用程式的設定時，會使用兩個連字號，並將其交換為冒號。

   下列秘密適用于範例應用程式。 這些值包含後置詞 `_prod` ，可區別這些值與 `_dev` 開發環境中從使用者秘密載入的尾碼值。 將取代 `{KEY VAULT NAME}` 為您在先前步驟中建立的金鑰保存庫名稱：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>針對非 Azure 託管應用程式使用應用程式識別碼和 x.509 憑證

設定 Azure AD、Azure 金鑰保存庫和應用程式，以使用 Azure Active Directory 應用程式識別碼和 x.509 憑證來向金鑰保存庫進行驗證 **（當應用程式裝載于 Azure 之外**）。 如需詳細資訊，請參閱[關於金鑰、祕密和憑證](/azure/key-vault/about-keys-secrets-and-certificates)。

> [!NOTE]
> 雖然 Azure 中裝載的應用程式支援使用應用程式識別碼和 x.509 憑證，但我們建議在 azure 中裝載應用程式時，使用 [適用于 azure 資源的受控](#use-managed-identities-for-azure-resources) 識別。 受控識別不需要將憑證儲存在應用程式或開發環境中。

當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用應用程式識別碼和 x.509 憑證 `Certificate` 。

1. 建立 PKCS # 12 封存 (*.pfx*) 憑證。 建立憑證的選項包括 [Windows](/windows/desktop/seccrypto/makecert) 和 [OpenSSL](https://www.openssl.org/)上的 MakeCert。
1. 將憑證安裝到目前使用者的個人憑證存儲中。 將金鑰標示為可匯出是選擇性的。 請注意憑證的指紋，此憑證稍後會在此程式中使用。
1. 將 PKCS # 12 封存 (*.pfx*) 憑證匯出為 DER 編碼憑證 (*.cer*) 。
1. 使用 Azure AD (**應用程式** 註冊) 註冊應用程式。
1. 將 DER 編碼憑證 (*.cer*) 上傳至 Azure AD：
   1. 在 Azure AD 中選取應用程式。
   1. 流覽至 **憑證 & 秘密**。
   1. 選取 [ **上傳憑證** ] 以上傳包含公開金鑰的憑證。 *.Cer*、 *pem* 或 *.crt* 憑證是可接受的。
1. 將金鑰保存庫名稱、應用程式識別碼和憑證指紋儲存在應用程式的檔案中 *appsettings.json* 。
1. 在 Azure 入口網站中流覽至 **金鑰保存庫** 。
1. 使用 Azure Key Vault 區段，選取您在 [生產環境中的秘密儲存體](#secret-storage-in-the-production-environment-with-azure-key-vault) 中建立的金鑰保存庫。
1. 選取 [存取原則]。
1. 選取 [新增存取原則]。
1. 開啟 **秘密許可權** ，並提供具有 **取得** 和 **列出** 許可權的應用程式。
1. 選取 [ **選取主體** ]，然後依名稱選取已註冊的應用程式。 選取 [選取]  按鈕。
1. 選取 [確定]。
1. 選取 [儲存]。
1. 部署應用程式。

`Certificate`範例應用程式會從 `IConfigurationRoot` 與秘密名稱相同的名稱取得其設定值：

* 非階層式值：的值 `SecretName` 是使用取得 `config["SecretName"]` 。
* 階層值 (區段) ：使用 `:` (冒號) 標記法或 `GetSection` 擴充方法。 您可以使用下列其中一種方法來取得設定值：
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 憑證由作業系統管理。 應用程式會 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用檔案所提供的值來呼叫 *appsettings.json* ：

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

範例值：

* 金鑰保存庫名稱： `contosovault`
* 應用程式識別碼： `627e911e-43cc-61d4-992e-12db9c81b413`
* 憑證指紋： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

當您執行應用程式時，網頁會顯示已載入的秘密值。 在開發環境中，秘密值會以 `_dev` 尾碼載入。 在生產環境中，值會以後綴載入 `_prod` 。

## <a name="use-managed-identities-for-azure-resources"></a>使用適用于 Azure 資源的受控識別

**部署至 azure 的應用程式** 可以利用 [Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別，讓應用程式使用 azure AD 驗證來驗證 azure Key Vault，而不需要認證 (應用程式識別碼和密碼/用戶端密碼) 儲存在應用程式中。

當 `#define` *Program.cs* 檔頂端的語句設定為時，範例應用程式會使用適用于 Azure 資源的受控識別 `Managed` 。

將保存庫名稱輸入應用程式的檔案 *appsettings.json* 。 範例應用程式在設定為版本時，不需要 (用戶端密碼) 的應用程式識別碼和密碼 `Managed` ，因此您可以忽略這些設定專案。 應用程式會部署至 Azure，而 Azure 會使用儲存在檔案中的保存庫名稱來驗證應用程式，以存取 Azure Key Vault *appsettings.json* 。

將範例應用程式部署至 Azure App Service。

部署至 Azure App Service 的應用程式會在建立服務時自動向 Azure AD 註冊。 從部署中取得物件識別碼，以在下列命令中使用。 物件識別碼會顯示在 Azure 入口網站中 **Identity** App Service 的面板上。

使用 Azure CLI 和應用程式的物件識別碼，為應用程式提供 `list` `get` 存取金鑰保存庫的和許可權：

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

使用 Azure CLI、PowerShell 或 Azure 入口網站 **重新開機應用程式**。

範例應用程式：

* 建立 `AzureServiceTokenProvider` 沒有連接字串之類別的實例。 未提供連接字串時，提供者會嘗試從 Azure 資源的受控識別取得存取權杖。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 實例權杖回呼建立新的。
* 此 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 實例會與的預設執行一起使用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，以載入所有的秘密值，並以冒號取代雙虛線 (`--`)  (`:`) 的索引鍵名稱。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

金鑰保存庫名稱範例值： `contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

當您執行應用程式時，網頁會顯示已載入的秘密值。 在開發環境中，秘密值具有 `_dev` 尾碼，因為它們是由使用者密碼提供。 在生產環境中，值會以後綴載入， `_prod` 因為它們是由 Azure Key Vault 提供。

如果您收到 `Access denied` 錯誤，請確認應用程式已向 AZURE AD 註冊，並已提供金鑰保存庫的存取權。 確認您已在 Azure 中重新開機服務。

如需搭配受控識別和 Azure DevOps 管線使用提供者的詳細資訊，請參閱使用 [受控服務識別建立與 VM 的 Azure Resource Manager 服務](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)連線。

## <a name="use-a-key-name-prefix"></a>使用金鑰名稱前置詞

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 提供接受執行的多載，可 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 讓您控制如何將金鑰保存庫密碼轉換成設定金鑰。 例如，您可以根據您在應用程式啟動時所提供的前置詞值，執行介面以載入秘密值。 例如，這可讓您根據應用程式的版本載入秘密。

> [!WARNING]
> 請勿在金鑰保存庫秘密上使用前置詞，將多個應用程式的秘密放入相同的金鑰保存庫中，或將環境秘密 (例如， *開發* 和 *生產* 秘密) 至相同的保存庫。 建議不同的應用程式和開發/生產環境使用不同的金鑰保存庫，以隔離最高層級安全性的應用程式環境。

下列範例會在金鑰保存庫中建立秘密 (並且針對開發環境使用 Secret Manager 工具) `5000-AppSecret` 不允許在金鑰保存庫秘密名稱) 中使用 (期間。 此秘密代表應用程式版本5.0.0.0 的應用程式密碼。 針對另一個版本的應用程式，5.1.0.0 會將秘密新增至金鑰保存庫 (，並使用) 的「秘密管理員」工具 `5100-AppSecret` 。 每個應用程式版本都會將其已建立版本的秘密值載入至其設定中，並在 `AppSecret` 載入密碼時將其移除。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用自訂來呼叫 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

此 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 執行會回應秘密的版本前置詞，以將適當的秘密載入設定：

* `Load` 在名稱開頭為前置詞時載入秘密。 未載入其他秘密。
* `GetKey`:
  * 移除秘密名稱中的前置詞。
  * 使用的任何名稱取代兩個虛線 `KeyDelimiter` ，也就是設定 (中使用的分隔符號，通常是) 的冒號。 Azure Key Vault 不允許秘密名稱中有冒號。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`方法是由提供者演算法呼叫，此演算法會逐一查看保存庫秘密，以找出具有版本首碼的金鑰。 當找到版本首碼時 `Load` ，演算法會使用 `GetKey` 方法來傳回秘密名稱的設定名稱。 它會從秘密的名稱中去除版本前置詞，並傳回秘密名稱的其餘部分，以載入應用程式的設定名稱-值組。

當此方法實施時：

1. 應用程式的專案檔中所指定的應用程式版本。 在下列範例中，應用程式的版本會設定為 `5.0.0.0` ：

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. 確認 `<UserSecretsId>` 屬性存在於應用程式的專案檔中，其中 `{GUID}` 是使用者提供的 GUID：

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   使用 [秘密管理員工具](xref:security/app-secrets)在本機儲存下列秘密：

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 您可以使用下列 Azure CLI 命令，將秘密儲存在 Azure Key Vault 中：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 執行應用程式時，會載入金鑰保存庫秘密。 的字串秘密 `5000-AppSecret` 會與應用程式專案檔中所指定的應用程式版本相符 (`5.0.0.0`) 。

1. `5000`以虛線)  (的版本會從索引鍵名稱中移除。 在整個應用程式中，使用金鑰讀取設定會 `AppSecret` 載入秘密值。

1. 如果將專案檔中的應用程式版本變更為 `5.1.0.0` ，並再次執行應用程式，則傳回的秘密值會 `5.1.0.0_secret_value_dev` 在開發環境和生產環境中 `5.1.0.0_secret_value_prod` 。

> [!NOTE]
> 您也可以提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 實作為 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。 自訂用戶端允許跨應用程式共用用戶端的單一實例。

## <a name="bind-an-array-to-a-class"></a>將陣列繫結到類別

提供者能夠將設定值讀取至陣列，以系結至 POCO 陣列。

從允許金鑰包含冒號 () 分隔符號的設定來源進行讀取時 `:` ，會使用數值索引鍵區段來區分組成陣列的索引鍵 (`:0:` 、 `:1:` 、 &hellip; `:{n}:`) 。 如需詳細資訊，請參閱設定：將陣列系結 [至類別](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。

Azure Key Vault 金鑰不能使用冒號做為分隔符號。 本主題所述的方法會使用雙虛線 (`--`) 做為階層值的分隔符號 (區段) 。 陣列金鑰會儲存在 Azure Key Vault 中，並以雙連字號和數位鍵區段 (`--0--` 、 `--1--` &hellip; `--{n}--`) 。

檢查 JSON 檔案所提供的下列 [Serilog](https://serilog.net/) 記錄提供者設定。 陣列中定義了兩個物件常值 `WriteTo` ，它會反映兩個 Serilog *接收*，其描述記錄輸出的目的地：

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

先前 JSON 檔案中顯示的設定會使用雙虛線 (`--`) 標記法和數值區段，儲存在 Azure Key Vault 中：

| Key | 值 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>重載秘密

在呼叫之前，會快取秘密 `IConfigurationRoot.Reload()` 。 在執行之前，應用程式不會遵守金鑰保存庫中已過期、已停用和更新的秘密 `Reload` 。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>已停用和過期的秘密

停用和過期的秘密會擲回 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。 若要防止應用程式擲回，請使用不同的設定提供者來提供設定，或更新已停用或過期的密碼。

## <a name="troubleshoot"></a>疑難排解

當應用程式無法使用提供者載入設定時，會將錯誤訊息寫入 [ASP.NET 核心記錄基礎結構](xref:fundamentals/logging/index)。 下列情況會導致無法載入設定：

* Azure Active Directory 中的應用程式或憑證設定不正確。
* 金鑰保存庫不存在於 Azure Key Vault 中。
* 應用程式未獲授權存取金鑰保存庫。
* 存取原則不包含 `Get` 和 `List` 許可權。
* 在金鑰保存庫中，設定資料 (的名稱/值組) 不正確地命名、遺失、停用或過期。
* 應用程式有錯誤的金鑰保存庫名稱 (`KeyVaultName`) 、AZURE Ad 應用程式識別碼 (`AzureADApplicationId`) 或 azure ad 憑證指紋 (`AzureADCertThumbprint`) 。
* 在您嘗試載入的值的應用程式中，設定機碼 (名稱) 不正確。
* 將應用程式的存取原則新增至金鑰保存庫時，已建立原則，但未在 [**存取原則**] UI 中選取 [**儲存**] 按鈕。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/configuration/index>
* [Microsoft Azure： Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure： Key Vault 檔](/azure/key-vault/)
* [如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 類別](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [快速入門：使用 .NET Web 應用程式從 Azure Key Vault 設定及擷取祕密](/azure/key-vault/quick-create-net)
* [教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Windows 虛擬機器](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
