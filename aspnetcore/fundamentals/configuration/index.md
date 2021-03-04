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
ms.openlocfilehash: 24b4d5fc11d21dce4d9e0fd2f8f0dd2d45e82baa
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110075"
---
# <a name="configuration-in-aspnet-core"></a>ASP.NET Core 的設定

由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 中的設定是使用一或多個設定 [提供者](#cp)來執行。 設定提供者會使用各種設定來源從機碼值組讀取設定資料：

* 設定檔案，例如 *appsettings.json*
* 環境變數
* Azure 金鑰保存庫
* Azure 應用程式組態
* 命令列引數
* 自訂提供者、已安裝或已建立
* 目錄檔案
* 記憶體內部 .NET 物件

本主題提供 ASP.NET Core 中的設定相關資訊。 如需在主控台應用程式中使用設定的詳細資訊，請參閱 [.net](/dotnet/core/extensions/configuration)設定。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

<a name="default"></a>

## <a name="default-configuration"></a>預設組態

使用 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 建立的 ASP.NET Core web 應用程式會產生下列程式碼：

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 會以下列順序提供應用程式的預設組態：

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) ：加入現有的 `IConfiguration` 作為來源。 在預設設定案例中，新增 [主機](#hvac) 設定，並將其設定為 _應用程式_ 設定的第一個來源。
1. [appsettings.json](#appsettingsjson) 使用 [JSON 設定提供者](#file-configuration-provider)。
1. *appsettings。* `Environment`使用 [json 設定提供者](#file-configuration-provider)的 *json。* 例如， *appsettings*。***生產 * * _._json* 與 *appsettings*。 * * * 開發** _._json *。
1. 應用程式在環境中執行時的[應用程式秘密](xref:security/app-secrets) `Development` 。
1. 使用 [環境變數設定提供者](#evcp)的環境變數。
1. 使用 [命令列設定提供者](#command-line)的命令列引數。

稍後新增的設定提供者會覆寫先前的金鑰設定。 例如，如果 `MyKey` 在和環境中設定 *appsettings.json* ，則會使用環境值。 使用預設的設定提供者，  [命令列設定提供者](#clcp) 會覆寫所有其他提供者。

如需的詳細資訊 `CreateDefaultBuilder` ，請參閱 [預設 builder 設定](xref:fundamentals/host/generic-host#default-builder-settings)。

下列程式碼會依新增的順序顯示已啟用的設定提供者：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

請考慮下列檔案 *appsettings.json* ：

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

預設會 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 以下列順序載入設定：

1. *appsettings.json*
1. *appsettings。* `Environment`*. json* ：例如 *appsettings*。***生產 * * _._json* 和 *appsettings**** * * 開發 _._json * 檔案。 檔案的環境版本是根據 [IHostingEnvironment EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)載入。 如需詳細資訊，請參閱<xref:fundamentals/environments>。

*appsettings*... `Environment`*json* 值會覆寫中的索引鍵 *appsettings.json* 。 例如，根據預設：

* 在開發期間， *appsettings*. ***開發** _._json * 設定會覆寫在中找到的值 *appsettings.json* 。
* 在生產環境中， *appsettings*. ***生產** _._json * 設定會覆寫在中找到的值 *appsettings.json* 。 例如，將應用程式部署至 Azure 時。

如果必須保證設定值，請參閱 [GetValue](#getvalue)。 上述範例只會讀取字串，且不支援預設值

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>使用選項模式系結階層式設定資料

[!INCLUDE[](~/includes/bind.md)]

使用 [預設](#default)設定， *appsettings.json* 以及 *appsettings。* `Environment`已啟用 [reloadOnChange： true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)的 *json* 檔案。 對和 appsettings 進行的變更 *appsettings.json* *。* `Environment`json 設定 [提供者](#jcp)會讀取應用程式啟動 ***後*** 的 *json* 檔案。

如需新增其他 JSON 設定檔的相關資訊，請參閱本檔中的 [json 設定提供者](#jcp) 。

## <a name="combining-service-collection"></a>合併服務集合

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a>安全性與使用者秘密

設定資料指導方針：

* 永遠不要將密碼或其他敏感性資料儲存在設定提供者程式碼或純文字設定檔中。 [秘密管理員](xref:security/app-secrets)工具可以用來在開發中儲存秘密。
* 不要在開發或測試環境中使用生產環境祕密。
* 請在專案外部指定祕密，以防止其意外認可至開放原始碼存放庫。

根據 [預設](#default)，使用者秘密設定來源會在 JSON 設定來源之後註冊。 因此，使用者秘密金鑰優先于和 appsettings 中的金鑰 *appsettings.json* *。* `Environment`*. json*。

如需有關儲存密碼或其他敏感性資料的詳細資訊：

* <xref:fundamentals/environments>
* <xref:security/app-secrets>：包含使用環境變數來儲存敏感性資料的建議。 秘密管理員工具會使用檔案設定 [提供者](#fcp) ，將使用者秘密儲存在本機系統上的 JSON 檔案中。

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可安全地儲存 ASP.NET Core 應用程式的應用程式祕密。 如需詳細資訊，請參閱<xref:security/key-vault-configuration>。

<a name="evcp"></a>

## <a name="environment-variables"></a>環境變數

使用 [預設](#default)設定時，會在 <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 讀取之後從環境變數機碼值組載入設定 *appsettings.json* （ *appsettings）。* `Environment`*. json* 和 [使用者秘密](xref:security/app-secrets)。 因此，從環境讀取的索引鍵值會覆寫 appsettings 中讀取的值 *appsettings.json* *。* `Environment`*. json* 和使用者秘密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

下列 `set` 命令：

* 在 Windows 上設定 [上述範例](#appsettingsjson) 的環境索引鍵和值。
* 使用 [範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)時，測試設定。 `dotnet run`命令必須在專案目錄中執行。

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

先前的環境設定：

* 只會在從其設定的命令視窗啟動的進程中設定。
* 使用 Visual Studio 啟動的瀏覽器不會讀取。

您可以使用下列的 [setx](/windows-server/administration/windows-commands/setx) 命令，在 Windows 上設定環境機碼和值。 與不同 `set` 的 `setx` 是，設定會持續保存。 `/M` 設定系統內容中的變數。 如果 `/M` 未使用此參數，則會設定使用者環境變數。

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

若要測試上述命令是否覆寫 *appsettings.json* 並 *appsettings。* `Environment`*. json*：

* 使用 Visual Studio：結束並重新啟動 Visual Studio。
* 使用 CLI：啟動新的命令視窗，然後輸入 `dotnet run` 。

<xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>使用字串呼叫以指定環境變數的前置詞：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

在上述程式碼中：

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 會在預設設定 [提供者](#default)之後加入。 如需排序設定提供者的範例，請參閱 [JSON 設定提供者](#jcp)。
* 具有前置詞的環境變數會覆 `MyCustomPrefix_` 寫預設的設定 [提供者](#default)。 這包括沒有前置詞的環境變數。

讀取設定機碼值組時，會移除前置詞。

下列命令會測試自訂前置詞：

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

[預設](#default)設定會載入前面加上和的環境變數和命令列引數 `DOTNET_` `ASPNETCORE_` 。 `DOTNET_` `ASPNETCORE_` ASP.NET Core 會使用和首碼來進行[主機和應用程式](xref:fundamentals/host/generic-host#host-configuration)設定，但不會用於使用者設定。 如需有關主機和應用程式設定的詳細資訊，請參閱 [.Net 泛型主機](xref:fundamentals/host/generic-host)。

在 [Azure App Service](https://azure.microsoft.com/services/app-service/)的 [**設定] > 設定**] 頁面上，選取 [**新增應用程式設定**]。 Azure App Service 應用程式設定如下：

* 靜態加密，並透過加密通道傳輸。
* 公開為環境變數。

如需詳細資訊，請參閱 [Azure App：使用 Azure 入口網站覆寫應用程式設定](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。

如需 Azure 資料庫連接字串的相關資訊，請參閱 [連接字串](#constr) 前置詞。

### <a name="naming-of-environment-variables"></a>環境變數的命名

環境變數名稱會反映檔案的結構 *appsettings.json* 。 階層中的每個元素會以雙底線分隔 (偏好) 或冒號。 當元素結構包含陣列時，應該將陣列索引視為這個路徑中的其他元素名稱。 請考慮下列檔案 *appsettings.json* 及其對等的值（以環境變數表示）。

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

**環境變數**

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a>在產生的 launchSettings.js中設定的環境變數

在 *launchSettings.js* 中設定的環境變數會覆寫系統內容中所設定的環境變數。 例如，ASP.NET Core web 範本會在將端點設定設定為的檔案 *上產生launchSettings.js* ：

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

設定會 `applicationUrl` 設定 `ASPNETCORE_URLS` 環境變數，並覆寫環境中設定的值。

### <a name="escape-environment-variables-on-linux"></a>在 Linux 上的 Escape 環境變數

在 Linux 上，必須將 URL 環境變數的值換成 `systemd` 可加以剖析。 使用產生的 linux 工具 `systemd-escape``http:--localhost:5001`
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a>顯示環境變數

下列程式碼會顯示應用程式啟動時的環境變數和值，這在進行環境設定的偵錯工具時很有用：

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a>命令列

使用 [預設](#default) 設定時，會 <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 從命令列引數索引鍵/值組在下列設定來源之後載入設定：

* *appsettings.json* 和 *appsettings*。 `Environment`*json* 檔案。
* 開發環境中的[應用程式秘密](xref:security/app-secrets)。
* 環境變數。

根據 [預設](#default)，在命令列覆寫設定值設定的設定值會與所有其他設定提供者一起設定。

### <a name="command-line-arguments"></a>命令列引數

下列命令會使用下列命令來設定索引鍵和值 `=` ：

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

下列命令會使用下列命令來設定索引鍵和值 `/` ：

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

下列命令會使用下列命令來設定索引鍵和值 `--` ：

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

索引鍵值：

* 必須遵循 `=` ，或者 `--` `/` 當值在空格後面時，索引鍵必須有或的前置詞。
* 如果 `=` 使用，則不需要。 例如： `MySetting=` 。

在相同的命令中，不要混用 `=` 與使用空格的索引鍵/值組搭配使用的命令列引數索引鍵/值配對。

### <a name="switch-mappings"></a>切換對應

交換器對應允許索引 **鍵** 名稱取代邏輯。 為方法提供切換取代的字典 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 。

使用切換對應字典時，會檢查字典中是否有任何索引鍵符合命令列引數所提供的索引鍵。 如果在字典中找到命令列索引鍵，則會傳回字典值以將索引鍵/值組設定為應用程式的設定。 所有前面加上單虛線 (`-`) 的命令列索引鍵都需要切換對應。

切換對應字典索引鍵規則：

* 參數的開頭必須是 `-` 或 `--` 。
* 切換對應字典不能包含重複索引鍵。

若要使用切換對應字典，請將它傳遞到的呼叫中 `AddCommandLine` ：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

下列程式碼顯示已取代金鑰的索引鍵值：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

下列命令適用于測試金鑰取代：

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

針對使用切換對應的應用程式，呼叫 `CreateDefaultBuilder` 不應傳遞引數。 `CreateDefaultBuilder`方法的 `AddCommandLine` 呼叫不包含對應的參數，而且沒有任何方法可將切換對應字典傳遞給 `CreateDefaultBuilder` 。 解決方案不會將引數傳遞至， `CreateDefaultBuilder` 而是會允許 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法處理引數和切換對應字典。

## <a name="hierarchical-configuration-data"></a>階層式設定資料

設定 API 會透過使用設定金鑰中的分隔符號來簡維階層式資料，以讀取階層式設定資料。

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列檔案 *appsettings.json* ：

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示數個設定設定：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

讀取階層式設定資料的慣用方式是使用選項模式。 如需詳細資訊，請參閱本檔中的系結階層式設定 [資料](#optpat) 。

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 與 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用來在設定資料中隔離區段與區段的子系。 [GetSection,、 GetChildren 與 Exists](#getsection) 中說明這些方法。

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>設定機碼和值

設定金鑰：

* 不區分大小寫。 例如，`ConnectionString` 與 `connectionstring` 會被視為相等的機碼。
* 如果在多個設定提供者中設定索引鍵和值，則會使用最後新增的提供者所提供的值。 如需詳細資訊，請參閱 [預設](#default)設定。
* 階層式機碼
  * 在設定 API 內，冒號分隔字元 (`:`) 可在所有平台上運作。
  * 在環境變數中，冒號分隔字元可能無法在所有平台上運作。 所有平臺都支援雙底線、 `__` ，而且會自動轉換為冒號 `:` 。
  * 在 Azure Key Vault 中，階層式索引鍵會使用 `--` 做為分隔符號。 [](xref:security/key-vault-configuration) `--` `:` 當秘密載入至應用程式的設定時，Azure Key Vault 設定提供者會自動以取代。
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支援在設定機碼中使用陣列索引將陣列繫結到物件。 [將陣列繫結到類別](#boa)一節說明陣列繫結。

設定值：

* 為字串。
* Null 值無法存放在設定中或繫結到物件。

<a name="cp"></a>

## <a name="configuration-providers"></a>設定提供者

下表顯示可供 ASP.NET Core 應用程式使用的設定提供者。

| 提供者 | 從提供設定 |
| -------- | ----------------------------------- |
| [Azure Key Vault 設定提供者](xref:security/key-vault-configuration) | Azure 金鑰保存庫 |
| [Azure 應用程式設定提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure 應用程式組態 |
| [命令列設定提供者](#clcp) | 命令列參數 |
| [自訂設定提供者](#custom-configuration-provider) | 自訂來源 |
| [環境變數設定提供者](#evcp) | 環境變數 |
| [檔案設定提供者](#file-configuration-provider) | INI、JSON 和 XML 檔案 |
| [每個檔案的金鑰配置提供者](#key-per-file-configuration-provider) | 目錄檔案 |
| [記憶體設定提供者](#memory-configuration-provider) | 記憶體內集合 |
| [使用者祕密](xref:security/app-secrets) | 使用者設定檔目錄中的檔案 |

設定來源會依照其設定提供者的指定順序讀取。 在程式碼中訂購設定提供者，以符合應用程式所需之基礎設定來源的優先順序。

典型的設定提供者順序是：

1. *appsettings.json*
1. *appsettings*... `Environment`*json*
1. [使用者祕密](xref:security/app-secrets)
1. 使用 [環境變數設定提供者](#evcp)的環境變數。
1. 使用 [命令列設定提供者](#command-line-configuration-provider)的命令列引數。

常見的作法是將命令列設定提供者新增至一系列的提供者，以允許命令列引數覆寫其他提供者所設定的設定。

先前的提供者序列會用於 [預設](#default)設定中。

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>連接字串前置詞

設定 API 有四個連接字串環境變數的特殊處理規則。 這些連接字串涉及設定應用程式環境的 Azure 連接字串。 具有資料表中所顯示前置詞的環境變數會以 [預設](#default) 設定載入至應用程式，或不提供任何前置詞 `AddEnvironmentVariables` 。

| 連接字串前置詞 | 提供者 |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | 自訂提供者 |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

當探索到具有下表所顯示之任何四個前置詞的環境變數並將其載入到設定中時：

* 會透過移除環境變數前置詞並新增設定機碼區段 (`ConnectionStrings`) 來移除具有下表顯示之前置詞的環境變數。
* 會建立新的設定機碼值組以代表資料庫連線提供者 (`CUSTOMCONNSTR_` 除外，這沒有所述提供者)。

| 環境變數機碼 | 已轉換的設定機碼 | 提供者設定項目                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 設定項目未建立。                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | 機碼：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 機碼：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 機碼：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`System.Data.SqlClient`  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>檔案設定提供者

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是用於從檔案系統載入設定的基底類別。 以下是衍生自的設定提供者 `FileConfigurationProvider` ：

* [INI 設定提供者](#ini-configuration-provider)
* [JSON 設定提供者](#jcp)
* [XML 設定提供者](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI 設定提供者

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 會在執行階段從 INI 檔案機碼值組載入設定。

下列程式碼會清除所有設定提供者，並新增數個設定提供者：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

在上述程式碼中， *MyIniConfig.ini* 和  *MyIniConfig* 中的設定 `Environment` 。中的設定會覆寫 *ini* 檔案：

* [環境變數設定提供者](#evcp)
* [命令列設定提供者](#clcp)。

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列 *MyIniConfig.ini* 檔案：

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>JSON 設定提供者

會 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 從 JSON 檔案機碼值組載入設定。

多載可以指定：

* 檔案是否為選擇性。
* 檔案變更時是否要重新載入設定。

請考慮下列程式碼：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

上述程式碼：

* 設定 JSON 設定提供者，以使用下列選項來載入檔案 *上的MyConfig.js* ：
  * `optional: true`：檔案是選擇性的。
  * `reloadOnChange: true` ：儲存變更時，會重載該檔案。
* 在 *MyConfig.json* 檔案之前讀取 [預設設定提供者](#default)。 預設設定提供者中的 [檔案覆寫] *MyConfig.js* 設定，包括 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)。

您通常 ***不*** 希望自訂 JSON 檔案覆寫 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)中所設定的值。

下列程式碼會清除所有設定提供者，並新增數個設定提供者：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

在上述程式碼中， *MyConfig.js* 中的設定和 *myconfig.xml*。 `Environment`*json* 檔案：

* 覆寫 *appsettings.json* 和 *appsettings* `Environment` 中的設定。*json* 檔案。
* 由 [環境變數設定提供者](#evcp) 和 [命令列設定提供者](#clcp)中的設定覆寫。

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含檔案上的下列 *MyConfig.js* ：

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>XML 設定提供者

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 會在執行階段從 XML 檔案機碼值組載入設定。

下列程式碼會清除所有設定提供者，並新增數個設定提供者：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

在上述程式碼中， *MyXMLFile.xml* 和  *MyXMLFile* 中的設定 `Environment` 。中的設定會覆寫 *xml* 檔案：

* [環境變數設定提供者](#evcp)
* [命令列設定提供者](#clcp)。

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含下列 *MyXMLFile.xml* 檔案：

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述幾個設定設定：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

若 `name` 屬性是用來區別元素，則可以重複那些使用相同元素名稱的元素：

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

下列程式碼會讀取先前的設定檔，並顯示金鑰和值：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

屬性可用來提供值：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

先前的設定檔會使用 `value` 載入下列機碼：

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>每個檔案的金鑰配置提供者

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目錄的檔案做為設定機碼值組。 機碼是檔案名稱。 值包含檔案的內容。 每個檔案的每個檔案設定提供者都是在 Docker 裝載案例中使用。

若要啟用每個檔案機碼設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 延伸模組方法。 檔案的 `directoryPath` 必須是絕對路徑。

多載允許指定：

* 設定來源的 `Action<KeyPerFileConfigurationSource>` 委派。
* 目錄是否為選擇性與目錄的路徑。

雙底線 (`__`) 是做為檔案名稱中的設定金鑰分隔符號使用。 例如，檔案名稱 `Logging__LogLevel__System` 會產生設定金鑰 `Logging:LogLevel:System`。

建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>記憶體設定提供者

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用記憶體內集合做為設定機碼值組。

下列程式碼會將記憶體集合新增至設定系統：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的下列程式碼會顯示上述設定設定：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

在上述程式碼中， `config.AddInMemoryCollection(Dict)` 是在 [預設設定提供者](#default)之後加入。 如需排序設定提供者的範例，請參閱 [JSON 設定提供者](#jcp)。

請參閱使用 [將陣列](#boa) 系結到另一個範例 `MemoryConfigurationProvider` 。

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a>Kestrel 端點組態

Kestrel 特定的端點設定會覆寫所有 [跨伺服器](xref:fundamentals/servers/index) 端點設定。 跨伺服器端點設定包括：

  * [UseUrls](xref:fundamentals/host/web-host#server-urls)
  * `--urls`在[命令列](xref:fundamentals/configuration/index#command-line)上
  * [環境變數](xref:fundamentals/configuration/index#environment-variables)`ASPNETCORE_URLS`

請考慮 *appsettings.json* ASP.NET Core web 應用程式中使用的下列檔案：

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

在 ASP.NET Core web 應用程式中使用上述反白顯示的標記 ***，並*** 在命令列上啟動應用程式時，會使用下列跨伺服器端點設定：

`dotnet run --urls="https://localhost:7777"`

Kestrel 會系結至) 中為檔案 (設定的端點 *appsettings.json* `https://localhost:9999` ，而不是 `https://localhost:7777` 。

請考慮設定為環境變數的 Kestrel 特定端點：

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

在上述的環境變數中， `Https` 是 Kestrel 特定端點的名稱。 上述檔案 *appsettings.json* 也會定義名為的 Kestrel 特定端點 `Https` 。 依 [預設](#default-configuration)，在 appsettings 之後，會讀取使用 [環境變數設定提供者](#evcp)的環境變數 *。* `Environment`因此 *，先前* 的環境變數會用於 `Https` 端點。

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 從具有指定索引鍵的設定中解壓縮單一值，並將其轉換為指定的類型：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

在上述程式碼中，如果在設定 `NumberKey` 中找不到， `99` 就會使用的預設值。

## <a name="getsection-getchildren-and-exists"></a>GetSection、GetChildren 與 Exists

針對接下來的範例，請考慮下列 *MySubsection.json* file：

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

下列程式碼會將 *MySubsection.js* 新增至設定提供者：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 會傳回具有指定之子區段索引鍵的設定子區段。

下列程式碼會傳回的值 `section1` ：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

下列程式碼會傳回的值 `section2:subsection0` ：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection` 絕不會傳回 `null`。 若找不到相符的區段，會傳回空的 `IConfigurationSection`。

當 `GetSection` 傳回相符區段時，未填入 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>。 當區段存在時，會傳回 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 與 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>。

### <a name="getchildren-and-exists"></a>GetChildren 和 Exists

下列程式碼會呼叫 [IConfiguration GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) ，並傳回 `section2:subsection0` 下列值：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

上述程式碼會呼叫 [>microsoft.extensions.options.configurationextensions](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) ，以確認區段存在：

 <a name="boa"></a>

## <a name="bind-an-array"></a>系結陣列

[ConfigurationBinder](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)支援使用設定索引鍵中的陣列索引，將陣列系結至物件。 任何公開數位索引鍵區段的陣列格式都能夠將陣列系結至 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 類別陣列。

請考慮從 [範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中 *MyArray.js* ：

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

下列程式碼會將 *MyArray.js* 新增至設定提供者：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

下列程式碼會讀取設定，並顯示這些值：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

上述程式碼會傳回下列輸出：

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

在上述輸出中，索引3具有值 `value40` ，對應于 `"4": "value40",` *MyArray.js* 中的。 系結陣列索引是連續的，不會系結至設定索引鍵索引。 設定系結器無法系結 null 值，或在系結物件中建立 null 專案

下列程式碼會 `array:entries` 使用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 擴充方法載入設定：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

下列程式碼會讀取中的設定 `arrayDict` `Dictionary` ，並顯示這些值：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

上述程式碼會傳回下列輸出：

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

繫結物件中的索引 &num;3 存放 `array:4` 設定機碼與其 `value4` 的設定資料。 系結包含陣列的設定資料時，在建立物件時，會使用設定機碼中的陣列索引來反覆運算設定資料。 設定資料中不能保留 Null 值，當設定機碼中的陣列略過一或多個索引時，不會在繫結物件中建立 Null 值項目。

在系 &num; 結至實例之前，您可以藉 `ArrayExample` 由讀取索引 &num; 3 索引鍵/值組的任何設定提供者，在系結至實例之前，提供索引3的遺漏設定專案。 請考慮下列來自範例下載的檔案 *Value3.js* ：

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

下列程式碼包含與的 *Value3.js* 設定 `arrayDict` `Dictionary` ：

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

下列程式碼會讀取先前的設定，並顯示這些值：

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

上述程式碼會傳回下列輸出：

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

自訂設定提供者不需要實作陣列繫結。

## <a name="custom-configuration-provider"></a>自訂設定提供者

範例應用程式示範如何建立會使用 [Entity Framework (EF)](/ef/core/) 從資料庫讀取設定機碼值組的基本設定提供者。

提供者具有下列特性：

* EF 記憶體內資料庫會用於示範用途。 若要使用需要連接字串的資料庫，請實作第二個 `ConfigurationBuilder` 以從另一個設定提供者提供連接字串。
* 啟動時，該提供者會將資料庫資料表讀入到設定中。 該提供者不會以個別機碼為基礎查詢資料庫。
* 未實作變更時重新載入，因此在應用程式啟動後更新資料庫對應用程式設定沒有影響。

定義 `EFConfigurationValue` 實體來在資料庫中存放設定值。

*Models/EFConfigurationValue.cs*：

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

新增 `EFConfigurationContext` 以存放及存取已設定的值。

*EFConfigurationProvider/EFConfigurationContext.cs*：

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

建立會實作 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的類別。

*EFConfigurationProvider/EFConfigurationSource.cs*：

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

透過繼承自 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 來建立自訂設定提供者。 若資料庫是空的，設定提供者會初始化資料庫。 由於設定索引 [鍵不區分大小寫](#keys)，因此用來初始化資料庫的字典會以不區分大小寫的比較子來建立 ([stringcomparison. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 。

*EFConfigurationProvider/EFConfigurationProvider.cs*：

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration` 擴充方法允許新增設定來源到 `ConfigurationBuilder`。

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

下列程式碼顯示如何在 *Program.cs* 中使用自訂 `EFConfigurationProvider`：

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>在啟動時存取設定

下列程式碼會在方法中顯示設定資料 `Startup` ：

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

如需使用啟動方便方法來存取設定的範例，請參閱[應用程式啟動：方便方法](xref:fundamentals/startup#convenience-methods)。

## <a name="access-configuration-in-razor-pages"></a>存取頁面中的設定 Razor

下列程式碼會在頁面中顯示設定資料 Razor ：

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

在下列程式碼中， `MyOptions` 會新增至服務容器， <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 並系結至設定：

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

下列標記會使用指示詞 [`@inject`](xref:mvc/views/razor#inject) Razor 來解析和顯示選項值：

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>MVC view 檔案中的存取設定

下列程式碼會顯示 MVC 視圖中的設定資料：

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a>使用委派設定選項

在委派中設定的選項會覆寫設定提供者中所設定的值。

使用委派來設定選項，會示範為範例應用程式中的範例2。

在下列程式碼中， <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務會新增至服務容器。 它會使用委派來設定下列值 `MyOptions` ：

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

下列程式碼會顯示選項值：

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

在上述範例中，和的值 `Option1` `Option2` 是在中指定， *appsettings.json* 然後由設定的委派覆寫。

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>主機與應用程式組態的比較

設定及啟動應用程式之前，會先設定及啟動「主機」。 主機負責應用程式啟動和存留期管理。 應用程式與主機都是使用此主題中所述的設定提供者來設定的。 主機組態機碼/值組也會包含在應用程式的組態中。 如需有關當建置主機時如何使用設定提供者的詳細資訊，以及設定來源如何影響主機設定的詳細資訊，請參閱 <xref:fundamentals/index#host>。

<a name="dhc"></a>

## <a name="default-host-configuration"></a>預設主機設定

如需使用 [Web 主機](xref:fundamentals/host/web-host)時預設組態的詳細資料，請參閱[本主題的 ASP.NET Core 2.2 版本](?view=aspnetcore-2.2&preserve-view=true)。

* 主機組態的提供來源：
  * 前面加 `DOTNET_` 上 (的環境變數，例如， `DOTNET_ENVIRONMENT` 使用 [環境變數設定提供者](#environment-variables)) 。 載入設定機碼值組時，會移除前置詞 (`DOTNET_`)。
  * 使用 [命令列設定提供者](#command-line-configuration-provider)的命令列引數。
* 已建立 Web 主機預設組態 (`ConfigureWebHostDefaults`)：
  * Kestrel 會用作為網頁伺服器，並使用應用程式的組態提供者來設定。
  * 新增主機篩選中介軟體。
  * 如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境變數設定為 `true`，則會新增轉接的標頭中介軟體。
  * 啟用 IIS 整合。

## <a name="other-configuration"></a>其他設定

本主題僅適用于 *應用程式* 設定。 執行和裝載 ASP.NET Core 應用程式的其他層面，是使用本主題未涵蓋的設定檔來設定：

* *launch.js開啟* /*launchSettings.js* 為開發環境的工具設定檔，如下所述：
  * 在中 <xref:fundamentals/environments#development> 。
  * 在檔集中，用來為開發案例設定 ASP.NET Core 應用程式的檔案。
* *web.config* 是伺服器設定檔，如下列主題所述：
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

在 *launchSettings.js* 中設定的環境變數會覆寫系統內容中所設定的環境變數。

如需從舊版 ASP.NET 遷移應用程式設定的詳細資訊，請參閱 <xref:migration/proper-to-2x/index#store-configurations> 。

## <a name="add-configuration-from-an-external-assembly"></a>從外部組件新增設定

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 實作允許在啟動時從應用程式 `Startup` 類別外部的外部組件，針對應用程式新增增強功能。 如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。

## <a name="additional-resources"></a>其他資源

* [設定來源程式碼](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 中的應用程式設定是以由 *設定提供者* 所建立的機碼值組為基礎。 設定提供者會從各種設定來源將設定資料讀取到機碼值組中：

* Azure 金鑰保存庫
* Azure 應用程式組態
* 命令列引數
* 自訂提供者 (已安裝或已建立)
* 目錄檔案
* 環境變數
* 記憶體內部 .NET 物件
* 設定檔

一般組態提供者案例 ([microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) 的組態套件包含在 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)中。

下列程式碼範例與範例應用程式中的程式碼範例使用 <xref:Microsoft.Extensions.Configuration> 命名空間：

```csharp
using Microsoft.Extensions.Configuration;
```

*選項模式* 是此主題中所述之設定概念的延伸。 選項使用類別來代表一組相關的設定。 如需詳細資訊，請參閱<xref:fundamentals/configuration/options>。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="host-versus-app-configuration"></a>主機與應用程式組態的比較

設定及啟動應用程式之前，會先設定及啟動「主機」。 主機負責應用程式啟動和存留期管理。 應用程式與主機都是使用此主題中所述的設定提供者來設定的。 主機組態機碼/值組也會包含在應用程式的組態中。 如需有關當建置主機時如何使用設定提供者的詳細資訊，以及設定來源如何影響主機設定的詳細資訊，請參閱 <xref:fundamentals/index#host>。

## <a name="other-configuration"></a>其他設定

本主題僅適用于 *應用程式* 設定。 執行和裝載 ASP.NET Core 應用程式的其他層面，是使用本主題未涵蓋的設定檔來設定：

* *launch.js開啟* /*launchSettings.js* 為開發環境的工具設定檔，如下所述：
  * 在中 <xref:fundamentals/environments#development> 。
  * 在檔集中，用來為開發案例設定 ASP.NET Core 應用程式的檔案。
* *web.config* 是伺服器設定檔，如下列主題所述：
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

如需從舊版 ASP.NET 遷移應用程式設定的詳細資訊，請參閱 <xref:migration/proper-to-2x/index#store-configurations> 。

## <a name="default-configuration"></a>預設組態

以 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 範本為基礎的 Web 應用程式，會在建置主機時呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。 `CreateDefaultBuilder` 會以下列順序提供應用程式的預設組態：

下列項目適用於使用 [Web 主機](xref:fundamentals/host/web-host)的應用程式。 如需使用[一般主機](xref:fundamentals/host/generic-host)時預設組態的詳細資料，請參閱[本主題的最新版本](xref:fundamentals/configuration/index)。

* 主機組態的提供來源：
  * 使用[環境變數組態提供者](#environment-variables-configuration-provider)且以 `ASPNETCORE_` 為前置詞 (例如 `ASPNETCORE_ENVIRONMENT`) 的環境變數。 載入設定機碼值組時，會移除前置詞 (`ASPNETCORE_`)。
  * 使用[命令列組態提供者](#command-line-configuration-provider)的命令列引數。
* 應用程式設定的提供來源：
  * *appsettings.json* 使用檔案設定 [提供者](#file-configuration-provider)。
  * 使用 [檔案組態提供者](#file-configuration-provider)的 *appsettings.{Environment}.json*。
  * 應用程式在使用輸入組件的 `Development` 環境中執行時的[使用者密碼](xref:security/app-secrets)。
  * 使用[環境變數組態提供者](#environment-variables-configuration-provider)的環境變數。
  * 使用[命令列組態提供者](#command-line-configuration-provider)的命令列引數。

## <a name="security"></a>安全性

採用下列做法來保護敏感性組態資料：

* 永遠不要將密碼或其他敏感性資料儲存在設定提供者程式碼或純文字設定檔中。
* 不要在開發或測試環境中使用生產環境祕密。
* 請在專案外部指定祕密，以防止其意外認可至開放原始碼存放庫。

如需詳細資訊，請參閱下列主題：

* <xref:fundamentals/environments>
* <xref:security/app-secrets>：包含使用環境變數來儲存敏感性資料的建議。 秘密管理員工具會使用檔案設定提供者，將使用者秘密儲存在本機系統上的 JSON 檔案中。 此主題稍後將說明「檔案設定提供者」。

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可安全地儲存 ASP.NET Core 應用程式的應用程式祕密。 如需詳細資訊，請參閱<xref:security/key-vault-configuration>。

## <a name="hierarchical-configuration-data"></a>階層式設定資料

設定 API 可在設定機碼中使用分隔符號來壓平合併階層式資料，以管理階層式設定資料。

在下列 JSON 檔案中，兩個區段的結構式階層中有四個機碼存在：

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

當該檔案被讀入設定之後，會建立唯一機碼來維護設定來源的原始階層式資料結構。 系統會使用冒號 (`:`) 將區段與機碼壓平合併以維護原始結構：

* section0:key0
* section0:key1
* section1:key0
* section1:key1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 與 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用來在設定資料中隔離區段與區段的子系。 [GetSection,、 GetChildren 與 Exists](#getsection-getchildren-and-exists) 中說明這些方法。

## <a name="conventions"></a>慣例

### <a name="sources-and-providers"></a>來源和提供者

在應用程式啟動時，會依照設定來源的設定提供者的指定順序讀入設定來源。

實作變更偵測的組態提供者能夠在基礎設定變更時重新載入組態。 例如，檔案組態提供者 (將於本主題稍後討論) 和 [Azure Key Vault 組態提供者](xref:security/key-vault-configuration)均會實作變更偵測。

您可以在應用程式的[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器中找到 <xref:Microsoft.Extensions.Configuration.IConfiguration>。 <xref:Microsoft.Extensions.Configuration.IConfiguration> 可以插入至 Razor 頁面 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> ，以取得類別的設定。

在下列範例中， `_config` 會使用欄位來存取設定值：

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

設定提供者無法使用 DI，因為當它們由主機設定時，它無法使用。

### <a name="keys"></a>索引鍵

設定機碼會採用下列慣例：

* 機碼區分大小寫。 例如，`ConnectionString` 與 `connectionstring` 會被視為相等的機碼。
* 若相同機碼的值是由相同或不同設定提供者設定，在機碼上設定的最後一個值是所使用的值。 如需重複的 JSON 金鑰的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/extensions/issues/2381)。
* 階層式機碼
  * 在設定 API 內，冒號分隔字元 (`:`) 可在所有平台上運作。
  * 在環境變數中，冒號分隔字元可能無法在所有平台上運作。 所有平台都支援雙底線 (`__`)，且會自動轉換為冒號。
  * 在 Azure Key Vault 中，階層式機碼使用 `--` (兩個破折號) 來做為分隔符號。 撰寫程式碼，以在將秘密載入至應用程式的設定時，以冒號取代虛線。
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支援在設定機碼中使用陣列索引將陣列繫結到物件。 [將陣列繫結到類別](#bind-an-array-to-a-class)一節說明陣列繫結。

### <a name="values"></a>值

設定值會採用下列慣例：

* 值是字串。
* Null 值無法存放在設定中或繫結到物件。

## <a name="providers"></a>提供者

下表顯示可供 ASP.NET Core 應用程式使用的設定提供者。

| 提供者 | 從&hellip;提供設定 |
| -------- | ----------------------------------- |
| [Azure Key Vault 設定提供者](xref:security/key-vault-configuration) (*安全性* 主題) | Azure 金鑰保存庫 |
| [Azure 應用程式組態提供者](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure 文件) | Azure 應用程式組態 |
| [命令列設定提供者](#command-line-configuration-provider) | 命令列參數 |
| [自訂設定提供者](#custom-configuration-provider) | 自訂來源 |
| [環境變數設定提供者](#environment-variables-configuration-provider) | 環境變數 |
| [檔案設定提供者](#file-configuration-provider) | 檔案 (INI、JSON、XML) |
| [每個檔案機碼的設定提供者](#key-per-file-configuration-provider) | 目錄檔案 |
| [記憶體設定提供者](#memory-configuration-provider) | 記憶體內集合 |
|  (*安全性* 主題的 [使用者密碼](xref:security/app-secrets))  | 使用者設定檔目錄中的檔案 |

在啟動時，會依照設定來源的設定提供者的指定順序讀入設定來源。 本主題所描述的設定提供者會依字母順序描述，而不是依照程式碼排列順序。 在程式碼中訂購設定提供者，以符合應用程式所需之基礎設定來源的優先順序。

典型的設定提供者順序是：

1. 檔案 (*appsettings.json* ， *appsettings. {環境} json*，其中 `{Environment}` 是應用程式目前的裝載環境) 
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. 僅)  (開發環境的[使用者秘密](xref:security/app-secrets)
1. 環境變數
1. 命令列引數

將命令列組態提供者放在提供者序列結尾是常見做法，因為這樣可以讓命令列引數覆寫由其他提供者所設定的組態。

當使用初始化新的主機建立器時，會使用上述的提供者序列 `CreateDefaultBuilder` 。 如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。

## <a name="configure-the-host-builder-with-useconfiguration"></a>使用 UseConfiguration 設定主機建立器

若要設定主機建立器，請使用該組態在主機建立器上呼叫 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。

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

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定提供者，以及由 `CreateDefaultBuilder` 自動新增的設定提供者：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>使用命令列引數覆寫先前的組態

若要提供可使用命令列引數覆寫的應用程式組態，請最後呼叫 `AddCommandLine`：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>移除 >createdefaultbuilder 新增的提供者

若要移除新增的提供者 `CreateDefaultBuilder` ，請在[IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)上呼叫[Clear](/dotnet/api/system.collections.generic.icollection-1.clear) first：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>在應用程式啟動期間使用組態

應用程式啟動期間，可以使用 `ConfigureAppConfiguration` 中為應用程式提供的組態，包括 `Startup.ConfigureServices`。 如需詳細資訊，請參閱[在啟動期間存取組態](#access-configuration-during-startup)一節。

## <a name="command-line-configuration-provider"></a>命令列設定提供者

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 會在執行階段從命令列引數機碼值組載入設定。

為了啟用命令列設定，<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 延伸模組方法會在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫。

呼叫 `CreateDefaultBuilder(string [])` 時，會自動呼叫 `AddCommandLine`。 如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。

`CreateDefaultBuilder` 也會載入：

* 自和 appsettings 的選擇性設定 *appsettings.json* *。環境}. json* 檔案。
* 開發環境中的[使用者秘密](xref:security/app-secrets)。
* 環境變數。

`CreateDefaultBuilder` 會最後才新增命令列設定提供者。 在執行階段傳遞的命令列引數會覆寫由其它提供者所設定的設定。

`CreateDefaultBuilder` 會在建構主機時執行作業。 因此，由`CreateDefaultBuilder` 啟用的命令列設定可以影響主機的設定方式。

針對以 ASP.NET Core 範本為基礎的應用程式，`AddCommandLine` 已由 `CreateDefaultBuilder` 呼叫。 若要新增其他組態提供者並維持能夠使用命令列引數覆寫這些提供者組態的能力，請在 `ConfigureAppConfiguration` 中呼叫應用程式的其他提供者，且最後呼叫 `AddCommandLine`。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**範例**

範例應用程式利用靜態方便方法 `CreateDefaultBuilder` 的優勢來建置主機，這包括對 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的呼叫。

1. 從專案目錄中開啟命令提示字元。
1. 提供命令列引數給 `dotnet run` 命令 `dotnet run CommandLineKey=CommandLineValue`。
1. 在應用程式執行之後，開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。
1. 觀察輸出是否包含提供給 `dotnet run` 之設定命令列引數的機碼值組。

### <a name="arguments"></a>引數

當值後面接著空格時，值必須接著等號 (`=`)，或機碼必須有前置詞 (`--` 或 `/`)。 如果使用等號 (例如 `CommandLineKey=`)，則不需要此值。

| 索引鍵前置字元               | 範例                                                |
| ------------------------ | ------------------------------------------------------ |
| 沒有前置字元                | `CommandLineKey1=value1`                               |
| 雙虛線 (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| 正斜線 (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

在相同的命令中，請不要混合使用等號搭配使用空格之機碼值組的命令列引數。

範例命令：

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>切換對應

參數對應允許索引鍵名稱取代邏輯。 使用手動建立設定時 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> ，請提供方法的切換替代字典 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 。

使用切換對應字典時，會檢查字典中是否有任何索引鍵符合命令列引數所提供的索引鍵。 如果在字典中找到命令列索引鍵，則會傳回字典值 (索引鍵取代) 以在應用程式的設定中設定機碼值組。 所有前面加上單虛線 (`-`) 的命令列索引鍵都需要切換對應。

切換對應字典索引鍵規則：

* 切換必須以單虛線 (`-`) 或雙虛線 (`--`) 開頭。
* 切換對應字典不能包含重複索引鍵。

建立切換對應字典。 下列範例會建立兩個切換對應：

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

建立主機時，請使用切換對應字典呼叫 `AddCommandLine`：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

針對使用切換對應的應用程式，呼叫 `CreateDefaultBuilder` 不應傳遞引數。 `CreateDefaultBuilder` 方法的 `AddCommandLine` 呼叫不包括對應的切換，且沒有任何方式可以將切換對應字典傳遞給 `CreateDefaultBuilder`。 解決方案並非將引數傳遞給 `CreateDefaultBuilder`，而是允許 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法同時處理引數與切換對應字典。

建立切換對應字典之後，它會包含下表中所示的資料。

| Key       | 值             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

若啟動應用程式時使用切換對應機碼，設定會接收由字典提供之機碼上的設定值：

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

執行上述命令之後，設定包含下表中顯示的值。

| Key               | 值    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>環境變數設定提供者

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 會在執行階段從環境變數機碼值組載入設定。

若要啟用環境變數設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 延伸模組方法。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) 可讓您在 Azure 入口網站中設定環境變數，以使用環境變數設定提供者覆寫應用程式設定。 如需詳細資訊，請參閱 [Azure App：使用 Azure 入口網站覆寫應用程式設定](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。

使用 [Web 主機](xref:fundamentals/host/web-host)初始化新的主機建立器並呼叫 `CreateDefaultBuilder` 時，可使用 `AddEnvironmentVariables` 為[主機組態](#host-versus-app-configuration)載入字首為 `ASPNETCORE_` 的環境變數。 如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。

`CreateDefaultBuilder` 也會載入：

* 來自無首碼之環境變數的應用程式組態 (在未提供首碼的情況下呼叫 `AddEnvironmentVariables`)。
* 自和 appsettings 的選擇性設定 *appsettings.json* *。環境}. json* 檔案。
* 開發環境中的[使用者秘密](xref:security/app-secrets)。
* 命令列引數。

從使用者祕密與 *appsettings* 檔案建立設定之後，會呼叫「環境變數設定提供者」。 在此位置呼叫提供者可讓系統在執行階段讀取環境變數，以覆寫由使用者祕密與 *appsettings* 檔案所設定的設定。

若要從其他環境變數提供應用程式設定，請在中呼叫應用程式的其他提供者， `ConfigureAppConfiguration` 並使用前置詞來呼叫 `AddEnvironmentVariables` ：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

呼叫 `AddEnvironmentVariables` last 以允許具有指定前置詞的環境變數覆寫其他提供者的值。

**範例**

範例應用程式利用靜態方便方法 `CreateDefaultBuilder` 的優勢來建置主機，這包括對 `AddEnvironmentVariables` 的呼叫。

1. 執行範例應用程式。 開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。
1. 觀察輸出是否包含環境變數 `ENVIRONMENT` 的機碼值組。 值反映應用程式執行所在環境，在本機執行時，通常是 `Development`。

為縮短由應用程式轉譯的環境變數清單，應用程式會篩選環境變數。 請參閱範例應用程式的 *Pages/Index.cshtml.cs* 檔案。

若要將所有可用的環境變數公開給應用程式，請 `FilteredConfiguration` 將 *Pages/Index. .cs* 變更為下列內容：

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>首碼

在提供方法的前置詞時，會篩選載入至應用程式設定的環境變數 `AddEnvironmentVariables` 。 例如，若要篩選前置詞為 `CUSTOM_` 的環境變數，請提供前置詞給設定提供者：

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

建立設定機碼值組時，會移除前置詞。

建立主機建立器時，主機組態由環境變數提供。 如需這些環境變數所用前置詞的詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。

**連接字串前置詞**

設定 API 四個連接字串環境變數的特殊處理規則，這些這些環境變數牽涉到針對應用程式環境設定 Azure 連接字串。 若將前置詞提供給 `AddEnvironmentVariables`具有下表顯示之前置詞的環境變數。

| 連接字串前置詞 | 提供者 |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | 自訂提供者 |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

當探索到具有下表所顯示之任何四個前置詞的環境變數並將其載入到設定中時：

* 會透過移除環境變數前置詞並新增設定機碼區段 (`ConnectionStrings`) 來移除具有下表顯示之前置詞的環境變數。
* 會建立新的設定機碼值組以代表資料庫連線提供者 (`CUSTOMCONNSTR_` 除外，這沒有所述提供者)。

| 環境變數機碼 | 已轉換的設定機碼 | 提供者設定項目                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 設定項目未建立。                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | 機碼：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 機碼：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 機碼：`ConnectionStrings:{KEY}_ProviderName`：<br>值：`System.Data.SqlClient`  |

**範例**

在伺服器上建立自訂連接字串環境變數：

* 名稱：`CUSTOMCONNSTR_ReleaseDB`
* 值：`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

如果 `IConfiguration` 插入並指派給名為的欄位 `_config` ，請閱讀下列值：

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>檔案設定提供者

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是用於從檔案系統載入設定的基底類別。 下列設定提供者專用於特定檔案類型：

* [INI 設定提供者](#ini-configuration-provider)
* [JSON 設定提供者](#json-configuration-provider)
* [XML 設定提供者](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI 設定提供者

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 會在執行階段從 INI 檔案機碼值組載入設定。

若要啟用 INI 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 延伸模組方法。

冒號可用來做為 INI 檔案設定中的區段分隔符號。

多載允許指定：

* 檔案是否為選擇性。
* 檔案變更時是否要重新載入設定。
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。

建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

INI 設定檔的一般範例：

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

先前的設定檔會使用 `value` 載入下列機碼：

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>JSON 設定提供者

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 會在執行階段從 JSON 檔案機碼值組載入設定。

若要啟用 JSON 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 延伸模組方法。

多載允許指定：

* 檔案是否為選擇性。
* 檔案變更時是否要重新載入設定。
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。

`AddJsonFile` 使用初始化新的主機建立器時，會自動呼叫兩次 `CreateDefaultBuilder` 。 會呼叫此方法以從下列位置載入設定：

* *appsettings.json*：先讀取此檔案。 檔案的環境版本可以覆寫檔案所提供的值 *appsettings.json* 。
* *appsettings。{環境}. json*：檔案的環境版本是根據 [IHostingEnvironment EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)載入。

如需詳細資訊，請參閱[＜預設組態＞](#default-configuration)一節。

`CreateDefaultBuilder` 也會載入：

* 環境變數。
* 開發環境中的[使用者秘密](xref:security/app-secrets)。
* 命令列引數。

會先建立 JSON 設定提供者。 因此，使用者祕密、環境變數與命令列引數會覆寫由 *appsettings* 檔案設定的設定。

`ConfigureAppConfiguration`建立主機以針對和 appsettings 以外的檔案指定應用程式的設定時，請呼叫 *appsettings.json* *。 {環境}. json*：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**範例**

範例應用程式會利用靜態的便利性方法 `CreateDefaultBuilder` 來建立主機，其中包含兩個對的呼叫 `AddJsonFile` ：

* 從下列載入設定的第一個呼叫 `AddJsonFile` *appsettings.json* ：

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* 從 appsettings 載入設定的第二個呼叫 `AddJsonFile` *。 {環境}. json*。 針對範例應用程式中的 *appsettings.Development.js* ，會載入下列檔案：

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. 執行範例應用程式。 開啟瀏覽器以瀏覽位於 `http://localhost:5000` 的應用程式。
1. 輸出會根據應用程式的環境，包含設定的機碼值組。 金鑰的記錄層級 `Logging:LogLevel:Default` 是在 `Debug` 開發環境中執行應用程式時。
1. 在生產環境中再次執行範例應用程式：
   1. 開啟檔案的 *屬性/launchSettings.js* 。
   1. 在 `ConfigurationSample` 設定檔中，將環境變數的值變更 `ASPNETCORE_ENVIRONMENT` 為 `Production` 。
   1. 使用命令介面儲存檔案，並 `dotnet run` 在中執行應用程式。
1. *appsettings.Development.js* 中的設定不會再覆寫中的設定 *appsettings.json* 。 索引鍵的記錄層級 `Logging:LogLevel:Default` 為 `Warning` 。

### <a name="xml-configuration-provider"></a>XML 設定提供者

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 會在執行階段從 XML 檔案機碼值組載入設定。

若要啟用 XML 檔案設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 延伸模組方法。

多載允許指定：

* 檔案是否為選擇性。
* 檔案變更時是否要重新載入設定。
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> 是用於存取該檔案。

建立設定機碼值組時，會忽略設定檔案的根節點。 請勿在檔案中指定文件類型定義 (DTD) 或命名空間。

建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

XML 設定檔可以針對重複的區段使用相異元素名稱：

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

先前的設定檔會使用 `value` 載入下列機碼：

* section0:key0
* section0:key1
* section1:key0
* section1:key1

若 `name` 屬性是用來區別元素，則可以重複那些使用相同元素名稱的元素：

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

先前的設定檔會使用 `value` 載入下列機碼：

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

屬性可用來提供值：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

先前的設定檔會使用 `value` 載入下列機碼：

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>每個檔案機碼設定提供者

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目錄的檔案做為設定機碼值組。 機碼是檔案名稱。 值包含檔案的內容。 每個檔案機碼設定提供者是在 Docker 主機案例中使用。

若要啟用每個檔案機碼設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 延伸模組方法。 檔案的 `directoryPath` 必須是絕對路徑。

多載允許指定：

* 設定來源的 `Action<KeyPerFileConfigurationSource>` 委派。
* 目錄是否為選擇性與目錄的路徑。

雙底線 (`__`) 是做為檔案名稱中的設定金鑰分隔符號使用。 例如，檔案名稱 `Logging__LogLevel__System` 會產生設定金鑰 `Logging:LogLevel:System`。

建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>記憶體設定提供者

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用記憶體內集合做為設定機碼值組。

若要啟用記憶體內集合設定，請在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的執行個體上呼叫 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 延伸模組方法。

設定提供者可以使用 `IEnumerable<KeyValuePair<String,String>>` 來初始化。

建置主機時呼叫 `ConfigureAppConfiguration` 以指定應用程式的設定。

下列範例會建立組態字典：

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

字典會與 `AddInMemoryCollection` 的呼叫搭配使用以提供組態：

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 從具有指定索引鍵的設定中解壓縮單一值，並將其轉換為指定的 noncollection 類型。 多載接受預設值。

下列範例將：

* 從具有機碼 `NumberKey` 的組態擷取字串值。 若在組態機碼中找不到 `NumberKey`，則會使用預設值 `99`。
* 鍵入值為 `int`。
* 在 `NumberConfig` 屬性中儲存值供頁面使用。

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

## <a name="getsection-getchildren-and-exists"></a>GetSection、GetChildren 與 Exists

針對下面的範例，請考慮下列 JSON 檔案。 在兩個區段中找到四個機碼，其中一個包括子區段組：

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

當檔案被讀入到設定之後，會建立下列唯一階層式機碼以存放設定值：

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 會擷取具有所指定子區段機碼的設定子區段。

若要傳回 `section1` 中只包含一個機碼值組的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，請呼叫 `GetSection` 並提供區段名稱：

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` 沒有值，只有索引鍵和路徑。

同樣地，若要取得 `section2:subsection0` 中之機碼的值，請呼叫 `GetSection` 並提供區段路徑：

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` 絕不會傳回 `null`。 若找不到相符的區段，會傳回空的 `IConfigurationSection`。

當 `GetSection` 傳回相符區段時，未填入 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>。 當區段存在時，會傳回 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 與 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>。

### <a name="getchildren"></a>GetChildren

對 `section2` 上之 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 的呼叫會取得包括下列項目的 `IEnumerable<IConfigurationSection>`：

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Exists

使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 來判斷設定區段是否存在：

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

以範例資料為例， `sectionExists` 是 `false`，因未設定資料中沒有 `section2:subsection2` 區段。

## <a name="bind-to-an-object-graph"></a>繫結至物件圖形

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以繫結整個 POCO 物件圖形。 如同系結簡單的物件，只會系結公用讀取/寫入屬性。

範例包含 `TvShow` 模型，其物件圖形包括 `Metadata` 與 `Actors` 類別 (*Models/TvShow.cs*)：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

範例應用程式有 *tvshow.xml* 檔案，其中包含設定資料：

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

設定會被繫結到整個 `TvShow` 物件 (使用 `Bind` 方法)。 已繫結的執行個體會被指派給屬性以用於轉譯：

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 系結並傳回指定的型別。 `Get<T>` 比使用 `Bind` 更方便。 下列程式碼說明如何搭配 `Get<T>` 上述範例使用：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>將陣列繫結到類別

範例應用程式示範此節中解釋的概念。

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支援在設定機碼中使用陣列索引將陣列繫結到物件。 任何公開數位索引鍵區段 (、) 的陣列格式， `:0:` `:1:` &hellip; `:{n}:` 都能夠將陣列系結至 POCO 類別陣列。

> [!NOTE]
> 繫結是由慣例提供。 自訂設定提供者不需要實作陣列繫結。

**記憶體內陣列處理**

考慮下表中顯示的設定機碼與值。

| Key             | 值  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

這些機碼與值是使用「記憶體設定提供者」在範例應用程式中載入：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

陣列會跳過索引 &num;3 的值。 設定繫結程式無法繫結 Null 值或在已繫結的物件中建立 Null 項目，這在示範繫結此陣列的結果到某物件的時候已經很清楚。

在範例應用程式中，POCO 類別可用來存放繫結設定資料：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

設定資料已繫結到物件：

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 也可以使用語法，以產生更精簡的程式碼：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

繫結物件 (`ArrayExample`的執行個體) 會從設定接收陣列資料。

| `ArrayExample.Entries` 索引 | `ArrayExample.Entries` 值 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

繫結物件中的索引 &num;3 存放 `array:4` 設定機碼與其 `value4` 的設定資料。 當繫結包含陣列的設定資料時，設定機碼中的陣列索引只會用來列舉設定資料 (當建立物件時)。 設定資料中不能保留 Null 值，當設定機碼中的陣列略過一或多個索引時，不會在繫結物件中建立 Null 值項目。

由會在設定中產生正確機碼值組的任何設定提供者繫結到 `ArrayExample` 執行個體之前，可以提供索引 &num;3 缺少的設定項目。 若範例包含具有缺少之機碼值組的額外 JSON 設定提供者，`ArrayExample.Entries` 會符合完整的設定陣列：

*missing_value.json*：

```json
{
  "array:entries:3": "value3"
}
```

在 `ConfigureAppConfiguration` 中：

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

表格中顯示的機碼值組會載入到設定中。

| Key             | 值  |
| :-------------: | :----: |
| array:entries:3 | value3 |

在「JSON 設定提供者」包含索引 &num;3 的項目之後，若繫結 `ArrayExample` 類別執行個體，`ArrayExample.Entries` 陣列會包括這些值：

| `ArrayExample.Entries` 索引 | `ArrayExample.Entries` 值 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**JSON 陣列處理**

若 JSON 檔案包含陣列，會為具有以零為基礎之區段索引的陣列元素建立設定機碼。 在下列設定檔中，`subsection` 是陣列：

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

「JSON 設定提供者」會將設定資料讀入到下列機碼值組：

| Key                     | 值  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

在範例應用程式中，下列 POCO 類別可用來繫結設定機碼值組：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

在繫結之後，`JsonArrayExample.Key` 會存放值 `valueA`。 子區段值會存放在 POCO 陣列屬性 `Subsection` 中。

| `JsonArrayExample.Subsection` 索引 | `JsonArrayExample.Subsection` 值 |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>自訂設定提供者

範例應用程式示範如何建立會使用 [Entity Framework (EF)](/ef/core/) 從資料庫讀取設定機碼值組的基本設定提供者。

提供者具有下列特性：

* EF 記憶體內資料庫會用於示範用途。 若要使用需要連接字串的資料庫，請實作第二個 `ConfigurationBuilder` 以從另一個設定提供者提供連接字串。
* 啟動時，該提供者會將資料庫資料表讀入到設定中。 該提供者不會以個別機碼為基礎查詢資料庫。
* 未實作變更時重新載入，因此在應用程式啟動後更新資料庫對應用程式設定沒有影響。

定義 `EFConfigurationValue` 實體來在資料庫中存放設定值。

*Models/EFConfigurationValue.cs*：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

新增 `EFConfigurationContext` 以存放及存取已設定的值。

*EFConfigurationProvider/EFConfigurationContext.cs*：

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

建立會實作 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的類別。

*EFConfigurationProvider/EFConfigurationSource.cs*：

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

透過繼承自 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 來建立自訂設定提供者。 若資料庫是空的，設定提供者會初始化資料庫。

*EFConfigurationProvider/EFConfigurationProvider.cs*：

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration` 擴充方法允許新增設定來源到 `ConfigurationBuilder`。

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

下列程式碼顯示如何在 *Program.cs* 中使用自訂 `EFConfigurationProvider`：

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>在啟動期間存取組態

將 `IConfiguration` 插入到 `Startup` 建構函式，以存取 `Startup.ConfigureServices` 中的設定值。 若要存取 `Startup.Configure` 中的設定，請直接將 `IConfiguration` 插入到方法或從建構函式使用執行個體：

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

如需使用啟動方便方法來存取設定的範例，請參閱[應用程式啟動：方便方法](xref:fundamentals/startup#convenience-methods)。

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>存取 Razor 頁面頁面或 MVC 視圖中的設定

若要存取 Razor 頁面頁面或 MVC 視圖中的設定設定，請新增 [using](xref:mvc/views/razor#using) 指示詞 ([c # 參考：使用](/dotnet/csharp/language-reference/keywords/using-directive) [Microsoft.Extensions.Configuration 命名空間](xref:Microsoft.Extensions.Configuration) 的指示詞) ，然後插入 <xref:Microsoft.Extensions.Configuration.IConfiguration> 頁面或視圖中。

在 [ Razor 頁面] 頁面中：

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

在 MVC 檢視中：

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

## <a name="add-configuration-from-an-external-assembly"></a>從外部組件新增設定

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 實作允許在啟動時從應用程式 `Startup` 類別外部的外部組件，針對應用程式新增增強功能。 如需詳細資訊，請參閱<xref:fundamentals/configuration/platform-specific-configuration>。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/configuration/options>

::: moniker-end
