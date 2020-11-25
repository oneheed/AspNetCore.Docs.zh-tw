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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>在 ASP.NET Core 的開發中安全儲存應用程式秘密

::: moniker range=">= aspnetcore-3.0"

由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)及 [Scott Addie](https://github.com/scottaddie)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

本檔說明如何管理開發電腦上 ASP.NET Core 應用程式的敏感性資料。 絕對不要將密碼或其他敏感性資料儲存在原始程式碼中。 生產秘密不應用於開發或測試。 秘密不應與應用程式一起部署。 相反地，您應該透過像環境變數或 Azure Key Vault 一樣的控制方式來存取生產秘密。 您可以透過 [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)儲存及保護 Azure 測試與生產祕密。

## <a name="environment-variables"></a>環境變數

環境變數是用來避免在程式碼或本機設定檔案中儲存應用程式秘密。 環境變數會覆寫所有先前指定設定來源的設定值。

請考慮啟用 **個別使用者帳戶** 安全性的 ASP.NET Core web 應用程式。 具有該索引鍵的專案檔案中包含預設的資料庫連接字串 *appsettings.json* `DefaultConnection` 。 預設連接字串適用于 LocalDB，以使用者模式執行，且不需要密碼。 在應用程式部署期間， `DefaultConnection` 可以使用環境變數的值來覆寫金鑰值。 環境變數可以儲存具有敏感性認證的完整連接字串。

> [!WARNING]
> 環境變數通常會以純文字未加密的文字儲存。 如果電腦或進程遭到入侵，則不受信任的合作物件可以存取環境變數。 可能需要其他措施來防止洩漏使用者秘密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>秘密管理員

在 ASP.NET Core 專案的開發期間，秘密管理員工具會儲存機密資料。 在此內容中，有一段機密資料是應用程式秘密。 應用程式秘密會儲存在專案樹狀結構中的不同位置。 應用程式秘密會與特定專案建立關聯，或在數個專案之間共用。 應用程式秘密不會簽入原始檔控制中。

> [!WARNING]
> 秘密管理員工具不會加密儲存的密碼，也不應將其視為受信任的存放區。 這僅適用于開發用途。 金鑰和值會儲存在使用者設定檔目錄的 JSON 設定檔中。

## <a name="how-the-secret-manager-tool-works"></a>秘密管理員工具的運作方式

秘密管理員工具會隱藏執行詳細資料，例如儲存值的位置和方式。 您可以使用此工具，而不需要瞭解這些實作為詳細資料。 這些值會儲存在本機電腦的使用者設定檔資料夾中的 JSON 檔案中：

# <a name="windows"></a>[Windows](#tab/windows)

檔案系統路徑：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

檔案系統路徑：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在先前的檔案路徑中，以 `<user_secrets_id>` 專案檔中所 `UserSecretsId` 指定的值取代。

請勿撰寫相依于使用 Secret Manager 工具儲存的資料位置或格式的程式碼。 這些執行詳細資料可能會變更。 例如，秘密值不會加密，但未來可能是如此。

## <a name="enable-secret-storage"></a>啟用秘密儲存體

秘密管理員工具會操作儲存在使用者設定檔中的專案特定設定。

秘密管理員工具組含 `init` .NET Core SDK 3.0.100 或更新版本中的命令。 若要使用使用者密碼，請在專案目錄中執行下列命令：

```dotnetcli
dotnet user-secrets init
```

上述命令會 `UserSecretsId` 在專案檔的中加入 `PropertyGroup` 專案。 根據預設，的內部文字 `UserSecretsId` 為 GUID。 內部文字是任意的，但對專案而言是唯一的。

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

在 Visual Studio 中，以滑鼠右鍵按一下方案總管中的專案，然後從內容功能表中選取 [ **管理使用者密碼** ]。 這項手勢會將以 `UserSecretsId` GUID 填入的專案加入至專案檔。

## <a name="set-a-secret"></a>設定秘密

定義由機碼及其值組成的應用程式密碼。 此密碼與專案的值相關聯 `UserSecretsId` 。 例如，從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在上述範例中，冒號表示 `Movies` 為具有屬性的物件常值 `ServiceApiKey` 。

秘密管理員工具也可以從其他目錄使用。 您 `--project` 可以使用選項來提供專案檔所在的檔案系統路徑。 例如：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 中的 JSON 結構壓平合併

Visual Studio 的 [ **管理使用者密碼** ] 手勢會在文字編輯器中開啟檔案的 *secrets.js* 。 以要儲存的索引鍵/值組取代 *secrets.js* 的內容。 例如：

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 結構會在透過或修改之後進行壓平合併 `dotnet user-secrets remove` `dotnet user-secrets set` 。 例如，執行會折迭 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 物件常值。 修改過的檔案類似下列 JSON：

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>設定多個秘密

您可以使用管線將 JSON 傳送至命令來設定一批秘密 `set` 。 在下列範例中，檔案的內容 *input.js* 會以管道傳送至 `set` 命令。

# <a name="windows"></a>[Windows](#tab/windows)

開啟命令 shell，然後執行下列命令：

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

開啟命令 shell，然後執行下列命令：

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>存取秘密

若要存取秘密，請完成下列步驟：

1. [註冊使用者秘密設定來源](#register-the-user-secrets-configuration-source)
1. [透過設定 API 讀取秘密](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a>註冊使用者秘密設定來源

使用者密碼設定 [提供者](/dotnet/core/extensions/configuration-providers) 會使用 .NET 設定 [API](xref:fundamentals/configuration/index)來註冊適當的設定來源。

當專案呼叫時，使用者秘密設定來源會自動加入至開發模式 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 。 `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>當為時 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> 呼叫 <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

`CreateDefaultBuilder`未呼叫時，請在中呼叫以明確地新增使用者秘密設定來源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 。 `AddUserSecrets`只有當應用程式在開發環境中執行時才呼叫，如下列範例所示：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a>透過設定 API 讀取秘密

如果已登錄使用者秘密設定來源，則 .NET 設定 API 可以讀取秘密。 您可以使用「函式[插入](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)」來取得 .NET 設定 API 的存取權。 請考慮下列讀取金鑰的範例 `Movies:ServiceApiKey` ：

**Startup 類別：**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

**Razor 頁面頁面模型：**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

如需詳細資訊，請參閱 [啟動及[存取設定] Razor 頁面中](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)的[存取](xref:fundamentals/configuration/index#access-configuration-in-startup)設定。

## <a name="map-secrets-to-a-poco"></a>將秘密對應至 POCO

將整個物件常值對應至 POCO (具有屬性) 的簡單 .NET 類別適用于匯總相關的屬性。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

若要將上述秘密對應至 POCO，請使用 .NET 設定 API 的 [物件圖形](xref:fundamentals/configuration/index#bind-to-an-object-graph) 系結功能。 下列程式碼會系結至自訂 `MovieSettings` POCO 並存取 `ServiceApiKey` 屬性值：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`和 `Movies:ServiceApiKey` 密碼會對應到中的個別屬性 `MovieSettings` ：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>使用秘密取代字串

以純文字儲存密碼並非安全的。 例如，儲存在中的資料庫連接字串 *appsettings.json* 可能包含指定使用者的密碼：

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是將密碼儲存為秘密。 例如：

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`從的連接字串中移除機碼值組 *appsettings.json* 。 例如：

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

您可以在物件的屬性上設定秘密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成連接字串：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>列出秘密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets list
```

下列輸出會出現：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在上述範例中，索引鍵名稱中的冒號代表 *secrets.js* 中的物件階層。

## <a name="remove-a-single-secret"></a>移除單一秘密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

應用程式 *在檔案上的secrets.js* 已修改為移除與機碼相關聯的索引鍵/值組 `MoviesConnectionString` ：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` 顯示下列訊息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>移除所有秘密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets clear
```

應用程式的所有使用者密碼都已從 *secrets.js* 檔案中刪除：

```json
{}
```

`dotnet user-secrets list`執行會顯示下列訊息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他資源

* 請參閱 [這個問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) ，以取得從 IIS 存取使用者密碼的相關資訊。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)及 [Scott Addie](https://github.com/scottaddie)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

本檔說明如何管理開發電腦上 ASP.NET Core 應用程式的敏感性資料。 絕對不要將密碼或其他敏感性資料儲存在原始程式碼中。 生產秘密不應用於開發或測試。 秘密不應與應用程式一起部署。 相反地，您應該透過像環境變數或 Azure Key Vault 一樣的控制方式來存取生產秘密。 您可以透過 [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)儲存及保護 Azure 測試與生產祕密。

## <a name="environment-variables"></a>環境變數

環境變數是用來避免在程式碼或本機設定檔案中儲存應用程式秘密。 環境變數會覆寫所有先前指定設定來源的設定值。

請考慮啟用 **個別使用者帳戶** 安全性的 ASP.NET Core web 應用程式。 具有該索引鍵的專案檔案中包含預設的資料庫連接字串 *appsettings.json* `DefaultConnection` 。 預設連接字串適用于 LocalDB，以使用者模式執行，且不需要密碼。 在應用程式部署期間， `DefaultConnection` 可以使用環境變數的值來覆寫金鑰值。 環境變數可以儲存具有敏感性認證的完整連接字串。

> [!WARNING]
> 環境變數通常會以純文字未加密的文字儲存。 如果電腦或進程遭到入侵，則不受信任的合作物件可以存取環境變數。 可能需要其他措施來防止洩漏使用者秘密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>秘密管理員

在 ASP.NET Core 專案的開發期間，秘密管理員工具會儲存機密資料。 在此內容中，有一段機密資料是應用程式秘密。 應用程式秘密會儲存在專案樹狀結構中的不同位置。 應用程式秘密會與特定專案建立關聯，或在數個專案之間共用。 應用程式秘密不會簽入原始檔控制中。

> [!WARNING]
> 秘密管理員工具不會加密儲存的密碼，也不應將其視為受信任的存放區。 這僅適用于開發用途。 金鑰和值會儲存在使用者設定檔目錄的 JSON 設定檔中。

## <a name="how-the-secret-manager-tool-works"></a>秘密管理員工具的運作方式

秘密管理員工具會隱藏執行詳細資料，例如儲存值的位置和方式。 您可以使用此工具，而不需要瞭解這些實作為詳細資料。 這些值會儲存在本機電腦的使用者設定檔資料夾中的 JSON 檔案中：

# <a name="windows"></a>[Windows](#tab/windows)

檔案系統路徑：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

檔案系統路徑：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在先前的檔案路徑中，以 `<user_secrets_id>` 專案檔中所 `UserSecretsId` 指定的值取代。

請勿撰寫相依于使用 Secret Manager 工具儲存的資料位置或格式的程式碼。 這些執行詳細資料可能會變更。 例如，秘密值不會加密，但未來可能是如此。

## <a name="enable-secret-storage"></a>啟用秘密儲存體

秘密管理員工具會操作儲存在使用者設定檔中的專案特定設定。

若要使用使用者密碼，請在 `UserSecretsId` 專案檔中定義 `PropertyGroup` 專案。 的內部文字 `UserSecretsId` 是任意的，但對專案而言是唯一的。 開發人員通常會產生的 GUID `UserSecretsId` 。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> 在 Visual Studio 中，以滑鼠右鍵按一下方案總管中的專案，然後從內容功能表中選取 [ **管理使用者密碼** ]。 這項手勢會將以 `UserSecretsId` GUID 填入的專案加入至專案檔。

## <a name="set-a-secret"></a>設定秘密

定義由機碼及其值組成的應用程式密碼。 此密碼與專案的值相關聯 `UserSecretsId` 。 例如，從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在上述範例中，冒號表示 `Movies` 為具有屬性的物件常值 `ServiceApiKey` 。

秘密管理員工具也可以從其他目錄使用。 您 `--project` 可以使用選項來提供專案檔所在的檔案系統路徑。 例如：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 中的 JSON 結構壓平合併

Visual Studio 的 [ **管理使用者密碼** ] 手勢會在文字編輯器中開啟檔案的 *secrets.js* 。 以要儲存的索引鍵/值組取代 *secrets.js* 的內容。 例如：

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 結構會在透過或修改之後進行壓平合併 `dotnet user-secrets remove` `dotnet user-secrets set` 。 例如，執行會折迭 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 物件常值。 修改過的檔案類似下列 JSON：

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>設定多個秘密

您可以使用管線將 JSON 傳送至命令來設定一批秘密 `set` 。 在下列範例中，檔案的內容 *input.js* 會以管道傳送至 `set` 命令。

# <a name="windows"></a>[Windows](#tab/windows)

開啟命令 shell，然後執行下列命令：

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

開啟命令 shell，然後執行下列命令：

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>存取秘密

設定 [API](xref:fundamentals/configuration/index) 會提供使用者密碼的存取權。

如果您的專案是以 .NET Framework 為目標，請安裝 [Microsoft.Extensions.Configuration。Usersecrets.xml](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 套件。

在 ASP.NET Core 2.0 或更新版本中，當專案呼叫時，使用者密碼設定來源會自動加入至開發模式 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 。 `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>當為時 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 呼叫 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

若 `CreateDefaultBuilder` 未呼叫，請在函式中呼叫，以明確地新增使用者秘密設定來源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` 。 `AddUserSecrets`只有當應用程式在開發環境中執行時才呼叫，如下列範例所示：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

您可以透過 .NET 設定 API 來抓取使用者密碼：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>將秘密對應至 POCO

將整個物件常值對應至 POCO (具有屬性) 的簡單 .NET 類別適用于匯總相關的屬性。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

若要將上述秘密對應至 POCO，請使用 .NET 設定 API 的 [物件圖形](xref:fundamentals/configuration/index#bind-to-an-object-graph) 系結功能。 下列程式碼會系結至自訂 `MovieSettings` POCO 並存取 `ServiceApiKey` 屬性值：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`和 `Movies:ServiceApiKey` 密碼會對應到中的個別屬性 `MovieSettings` ：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>使用秘密取代字串

以純文字儲存密碼並非安全的。 例如，儲存在中的資料庫連接字串 *appsettings.json* 可能包含指定使用者的密碼：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是將密碼儲存為秘密。 例如：

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`從的連接字串中移除機碼值組 *appsettings.json* 。 例如：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

您可以在物件的屬性上設定秘密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成連接字串：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>列出秘密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets list
```

下列輸出會出現：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在上述範例中，索引鍵名稱中的冒號代表 *secrets.js* 中的物件階層。

## <a name="remove-a-single-secret"></a>移除單一秘密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

應用程式 *在檔案上的secrets.js* 已修改為移除與機碼相關聯的索引鍵/值組 `MoviesConnectionString` ：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`執行會顯示下列訊息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>移除所有秘密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

從專案檔所在的目錄中執行下列命令：

```dotnetcli
dotnet user-secrets clear
```

應用程式的所有使用者密碼都已從 *secrets.js* 檔案中刪除：

```json
{}
```

`dotnet user-secrets list`執行會顯示下列訊息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他資源

* 請參閱 [這個問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) ，以取得從 IIS 存取使用者密碼的相關資訊。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end