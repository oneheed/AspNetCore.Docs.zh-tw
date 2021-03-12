---
title: ASP.NET Core 中的金鑰儲存提供者
author: rick-anderson
description: 瞭解 ASP.NET Core 中的金鑰儲存提供者，以及如何設定金鑰儲存位置。
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 137cdabc12b7cd01b82f7fe9921c17a5be957eb7
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605525"
---
# <a name="key-storage-providers-in-aspnet-core"></a>ASP.NET Core 中的金鑰儲存提供者

根據預設，資料保護系統會 [採用探索機制](xref:security/data-protection/configuration/default-settings) ，來判斷應該保存密碼編譯金鑰的位置。 開發人員可以覆寫預設的探索機制，並手動指定位置。

> [!WARNING]
> 如果您指定明確的金鑰持續性位置，資料保護系統會取消註冊靜態的預設金鑰加密機制，因此金鑰不會再加密。 建議您另外針對生產部署 [指定明確的金鑰加密機制](xref:security/data-protection/implementation/key-encryption-at-rest) 。

## <a name="file-system"></a>檔案系統

若要設定以檔案系統為基礎的金鑰存放庫，請呼叫 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 設定常式，如下所示。 提供指向存放庫的 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) ，存放庫應儲存金鑰：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`可以指向本機電腦上的目錄，也可以指向網路共用上的資料夾。 如果指向本機電腦上的目錄 (且案例是只有本機電腦上的應用程式需要存取權才能使用此存放庫) ，請考慮在 Windows 上使用 [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) () 加密待用的金鑰。 否則，請考慮使用 [x.509 憑證](xref:security/data-protection/implementation/key-encryption-at-rest) 來加密待用的金鑰。

## <a name="azure-storage"></a>Azure 儲存體

[AspNetCore. DataProtection blob](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)封裝可讓您將資料保護金鑰儲存在 Azure Blob 儲存體中。 您可以在 web 應用程式的數個實例之間共用金鑰。 應用程式可以 cookie 在多部伺服器之間共用驗證或 CSRF 保護。

若要設定 Azure Blob 儲存體提供者，請呼叫其中一個 [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) 多載。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

如果 web 應用程式是以 Azure 服務的形式執行，則可以使用連接字串來 [透過 azure 儲存體](/dotnet/api/azure.storage.blobs.blobcontainerclient)驗證 azure 儲存體。

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> 您可以在 Azure 入口網站中的 [存取金鑰] 區段底下，或藉由執行下列 CLI 命令，在 Azure 入口網站中找到儲存體帳戶的連接字串： 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

[AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/)封裝可讓您將資料保護金鑰儲存在 Redis 快取中。 您可以在 web 應用程式的數個實例之間共用金鑰。 應用程式可以 cookie 在多部伺服器之間共用驗證或 CSRF 保護。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[AspNetCore. DataProtection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/)封裝可讓您將資料保護金鑰儲存在 Redis 快取中。 您可以在 web 應用程式的數個實例之間共用金鑰。 應用程式可以 cookie 在多部伺服器之間共用驗證或 CSRF 保護。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

若要在 Redis 上設定，請呼叫其中一個 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) 多載：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

若要在 Redis 上設定，請呼叫其中一個 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) 多載：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

如需詳細資訊，請參閱下列主題：

* [>stackexchange.redis. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/main/docs/Basics.md)
* [Azure Redis 快取](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [ASP.NET Core DataProtection 範例](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>登錄

**僅適用于 Windows 部署。**

有時候應用程式可能沒有檔案系統的寫入權限。 假設應用程式是以虛擬服務帳戶的形式執行， (例如 *w3wp.exe* 的應用程式集區身分識別) 。 在這些情況下，系統管理員可以布建可由服務帳戶身分識別存取的登錄機碼。 呼叫 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) 擴充方法，如下所示。 提供 [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) ，指向應儲存密碼編譯金鑰的位置：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys", true));
}
```

> [!IMPORTANT]
> 我們建議使用 [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) 來加密待用的金鑰。

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[AspNetCore. DataProtection. microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)套件提供使用 Entity Framework Core 將資料保護金鑰儲存至資料庫的機制。 `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`NuGet 套件必須新增至專案檔，它不是[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)的一部分。

透過此套件，您可以在 web 應用程式的多個實例之間共用金鑰。

若要設定 EF Core 提供者，請[呼叫 \<TContext> PersistKeysToDbCoNtext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)方法：

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

泛型參數 `TContext` 必須繼承自 [DbCoNtext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 並執行 [IDataProtectionKeyCoNtext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)：

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

建立 `DataProtectionKeys` 資料表。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 **套件管理員主控台** 中執行下列命令 (PMC) 視窗：

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

在命令 shell 中執行下列命令：

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` 是在 `DbContext` 上述程式碼範例中定義的。 如果您使用 `DbContext` 不同名稱的，請 `DbContext` 以您的名稱取代 `MyKeysContext` 。

`DataProtectionKeys`類別/實體採用下表所示的結構。

| 屬性/欄位 | CLR 型別 | SQL 型別              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`、PK、 `IDENTITY(1,1)` 、not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`空 |
| `Xml`          | `string` | `nvarchar(MAX)`空 |

::: moniker-end

## <a name="custom-key-repository"></a>自訂金鑰存放庫

如果不適合使用內建機制，開發人員可以藉由提供自訂 [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)來指定自己的金鑰持續性機制。