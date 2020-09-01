---
title: ASP.NET Core 中的金鑰管理擴充性
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護金鑰管理擴充性。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: db718b8d4c305b75ad52054efde6b2d03f6825ed
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153528"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core 中的金鑰管理擴充性

閱讀本節之前，請閱讀 [金鑰管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 區段，其說明這些 api 背後的一些基本概念。

**警告**：實作為下列任何介面的型別應該是多個呼叫端的安全線程。

## <a name="key"></a>Key

`IKey`介面是 cryptosystem 中索引鍵的基本標記法。 詞彙索引鍵是在這裡以抽象意義來使用，而不是在「密碼編譯金鑰內容」的常值意義中。 索引鍵具有下列屬性：

* 啟用、建立和到期日

* 撤銷狀態

* GUID (的金鑰識別碼) 

::: moniker range=">= aspnetcore-2.0"

此外， `IKey` `CreateEncryptor` 也會公開方法，可用來建立系結至此索引鍵的 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 實例。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

此外， `IKey` `CreateEncryptorInstance` 也會公開方法，可用來建立系結至此索引鍵的 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 實例。

::: moniker-end

> [!NOTE]
> 沒有 API 可從實例中取出原始的密碼編譯內容 `IKey` 。

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager`介面代表負責一般金鑰儲存、抓取和操作的物件。 它會公開三個高階作業：

* 建立新的金鑰，並將它保存在儲存體中。

* 取得儲存體中的所有金鑰。

* 撤銷一或多個金鑰，並將撤銷資訊保存到儲存體。

>[!WARNING]
> 撰寫 `IKeyManager` 是非常先進的工作，大部分的開發人員都不應該嘗試它。 相反地，大部分的開發人員都應該利用 [XmlKeyManager](#xmlkeymanager) 類別所提供的功能。

## <a name="xmlkeymanager"></a>XmlKeyManager

此 `XmlKeyManager` 類型是的內建實體實作為 `IKeyManager` 。 它提供數個實用的工具，包括待用金鑰的金鑰加密和加密。 這個系統中的索引鍵會以 XML 專案的形式呈現， (明確 [system.xml.linq.xelement>](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) 。

`XmlKeyManager` 在完成其工作的過程中，相依于其他幾個元件：

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`，表示新索引鍵所使用的演算法。

* `IXmlRepository`，可控制在儲存體中保存金鑰的位置。

* `IXmlEncryptor` [選用]，這可讓您加密待用的金鑰。

* `IKeyEscrowSink` [選用]，提供金鑰的可提供服務。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`，可控制在儲存體中保存金鑰的位置。

* `IXmlEncryptor` [選用]，這可讓您加密待用的金鑰。

* `IKeyEscrowSink` [選用]，提供金鑰的可提供服務。

::: moniker-end

以下是高層級的圖表，指出這些元件如何一起連接在一起 `XmlKeyManager` 。

::: moniker range=">= aspnetcore-2.0"

![建立金鑰](key-management/_static/keycreation2.png)

*金鑰建立/CreateNewKey*

在的執行中 `CreateNewKey` ， `AlgorithmConfiguration` 元件是用來建立唯一的 `IAuthenticatedEncryptorDescriptor` ，然後再序列化為 XML。 如果有金鑰內建接收， (則會將未經加密的未經加密) XML 提供給接收以進行長期儲存。 `IXmlEncryptor`如果需要) 產生加密的 xml 檔，則未加密的 xml 接著會透過 (執行。 此加密檔會透過來保存到長期儲存 `IXmlRepository` 。  (如果未 `IXmlEncryptor` 設定，則會將未加密的檔保存在中 `IXmlRepository` 。 ) 

![金鑰抓取](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![建立金鑰](key-management/_static/keycreation1.png)

*金鑰建立/CreateNewKey*

在的執行中 `CreateNewKey` ， `IAuthenticatedEncryptorConfiguration` 元件是用來建立唯一的 `IAuthenticatedEncryptorDescriptor` ，然後再序列化為 XML。 如果有金鑰內建接收， (則會將未經加密的未經加密) XML 提供給接收以進行長期儲存。 `IXmlEncryptor`如果需要) 產生加密的 xml 檔，則未加密的 xml 接著會透過 (執行。 此加密檔會透過來保存到長期儲存 `IXmlRepository` 。  (如果未 `IXmlEncryptor` 設定，則會將未加密的檔保存在中 `IXmlRepository` 。 ) 

![金鑰抓取](key-management/_static/keyretrieval1.png)

::: moniker-end

*金鑰抓取/GetAllKeys*

在的執行中 `GetAllKeys` ，代表索引鍵和撤銷的 XML 檔是從基礎讀取的 `IXmlRepository` 。 如果這些檔經過加密，系統會自動將它們解密。 `XmlKeyManager` 建立適當的 `IAuthenticatedEncryptorDescriptorDeserializer` 實例，以將檔案還原序列化回 `IAuthenticatedEncryptorDescriptor` 實例，然後包裝在個別 `IKey` 實例中。 此實例集合 `IKey` 會傳回給呼叫端。

如需特定 XML 元素的詳細資訊，請參閱 [金鑰儲存格式檔](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository`介面代表可保存 xml 的型別，並從備份存放區取出 xml。 它會公開兩個 Api：

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

的 `IXmlRepository` 執行不需要剖析傳遞的 XML。 它們應該會將 XML 檔視為不透明，並讓較高層的人員擔心產生和剖析檔。

有四個內建的實體型別，這些型別會執行 `IXmlRepository` ：

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

如需詳細資訊，請參閱 [金鑰儲存提供者檔](xref:security/data-protection/implementation/key-storage-providers) 。

`IXmlRepository`使用不同的備份存放區時，註冊自訂是適當的 (例如 Azure 資料表儲存體) 。

若要變更預設儲存機制的整個應用程式，請註冊自訂 `IXmlRepository` 實例：

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor`介面代表可以加密純文字 XML 專案的型別。 它會公開單一 API：

* 加密 (System.xml.linq.xelement> plaiNtextElement) ： EncryptedXmlInfo

如果序列化 `IAuthenticatedEncryptorDescriptor` 包含任何標記為「需要加密」的專案，則 `XmlKeyManager` 會透過設定的方法執行這些 `IXmlEncryptor` 元素 `Encrypt` ，並將 enciphered 專案（而非純文字元素）保存到 `IXmlRepository` 。 方法的輸出 `Encrypt` 是 `EncryptedXmlInfo` 物件。 此物件是包含結果 enciphered 的包裝函 `XElement` 式，以及表示 `IXmlDecryptor` 可用來解密對應元素的類型。

有四個內建的實體型別，這些型別會執行 `IXmlEncryptor` ：

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

如需詳細資訊，請參閱「 [靜態加密」檔](xref:security/data-protection/implementation/key-encryption-at-rest) 。

若要變更整個應用程式的預設金鑰靜態加密機制，請註冊自訂 `IXmlEncryptor` 實例：

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor`介面代表一種型別，它知道如何解密透過所 `XElement` enciphered 的 `IXmlEncryptor` 。 它會公開單一 API：

* 解密 (System.xml.linq.xelement> encryptedElement) ： System.xml.linq.xelement>

`Decrypt`方法會復原執行的加密 `IXmlEncryptor.Encrypt` 。 一般來說，每個具體 `IXmlEncryptor` 的執行都會有對應的具體 `IXmlDecryptor` 實作為。

要執行的型別 `IXmlDecryptor` 應該具有下列兩個公用函式的其中一個：

* .ctor (IServiceProvider) 
* .ctor ( # A1

> [!NOTE]
> `IServiceProvider`傳遞給此函式的會是 null。

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink`介面代表可執行機密資訊的型別。 回想一下，序列化描述項可能包含機密資訊 (例如加密內容) ，而這就是首次引進 [IXmlEncryptor](#ixmlencryptor) 型別的結果。 不過，發生意外狀況，而且可以刪除或損毀金鑰環形。

此證書處理介面提供了緊急的換用程式影線，可在任何已設定的 [IXmlEncryptor](#ixmlencryptor)轉換之前，允許存取原始序列化的 XML。 介面會公開單一 API：

* 儲存 (Guid keyId，System.xml.linq.xelement> 元素) 

它是由執行， `IKeyEscrowSink` 以安全的方式處理提供的專案，與商務原則一致。 其中一個可能的執行可能是，使用已知公司的 x.509 憑證來加密 XML 專案，而該憑證已委付憑證的私密金鑰; `CertificateXmlEncryptor` 此類型可以協助您。 此 `IKeyEscrowSink` 實也會負責適當保存提供的元素。

依預設，不會啟用任何的託管機制，但是伺服器管理員可以進行 [全域設定](xref:security/data-protection/configuration/machine-wide-policy)。 它也可以透過方法以程式設計方式來設定， `IDataProtectionBuilder.AddKeyEscrowSink` 如下列範例所示。 方法多載會 `AddKeyEscrowSink` 鏡像 `IServiceCollection.AddSingleton` 和多載 `IServiceCollection.AddInstance` ，因為 `IKeyEscrowSink` 實例應 singleton。 如果 `IKeyEscrowSink` 註冊了多個實例，每個實例都會在金鑰產生期間呼叫，因此可以同時將金鑰委付至多個機制。

沒有 API 可從實例讀取資料 `IKeyEscrowSink` 。 這與授權機制的設計理論一致：它的目的是要讓受信任的授權單位可以存取金鑰內容，而且因為應用程式本身不是受信任的授權單位，所以它不應該有自己的委付資料存取權。

下列範例程式碼示範如何建立和註冊 `IKeyEscrowSink` where 索引鍵的委付，如此一來，只有 "CONTOSODomain Admins" 的成員才能復原它們。

> [!NOTE]
> 若要執行此範例，您必須在已加入網域的 Windows 8/Windows Server 2012 電腦上，而且網域控制站必須是 Windows Server 2012 或更新版本。

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
