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
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="232ea-103">ASP.NET Core 中的金鑰管理擴充性</span><span class="sxs-lookup"><span data-stu-id="232ea-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="232ea-104">閱讀本節之前，請閱讀 [金鑰管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 區段，其說明這些 api 背後的一些基本概念。</span><span class="sxs-lookup"><span data-stu-id="232ea-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="232ea-105">**警告**：實作為下列任何介面的型別應該是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="232ea-105">**Warning**: Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="232ea-106">Key</span><span class="sxs-lookup"><span data-stu-id="232ea-106">Key</span></span>

<span data-ttu-id="232ea-107">`IKey`介面是 cryptosystem 中索引鍵的基本標記法。</span><span class="sxs-lookup"><span data-stu-id="232ea-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="232ea-108">詞彙索引鍵是在這裡以抽象意義來使用，而不是在「密碼編譯金鑰內容」的常值意義中。</span><span class="sxs-lookup"><span data-stu-id="232ea-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="232ea-109">索引鍵具有下列屬性：</span><span class="sxs-lookup"><span data-stu-id="232ea-109">A key has the following properties:</span></span>

* <span data-ttu-id="232ea-110">啟用、建立和到期日</span><span class="sxs-lookup"><span data-stu-id="232ea-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="232ea-111">撤銷狀態</span><span class="sxs-lookup"><span data-stu-id="232ea-111">Revocation status</span></span>

* <span data-ttu-id="232ea-112">GUID (的金鑰識別碼) </span><span class="sxs-lookup"><span data-stu-id="232ea-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="232ea-113">此外， `IKey` `CreateEncryptor` 也會公開方法，可用來建立系結至此索引鍵的 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 實例。</span><span class="sxs-lookup"><span data-stu-id="232ea-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="232ea-114">此外， `IKey` `CreateEncryptorInstance` 也會公開方法，可用來建立系結至此索引鍵的 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 實例。</span><span class="sxs-lookup"><span data-stu-id="232ea-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="232ea-115">沒有 API 可從實例中取出原始的密碼編譯內容 `IKey` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="232ea-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="232ea-116">IKeyManager</span></span>

<span data-ttu-id="232ea-117">`IKeyManager`介面代表負責一般金鑰儲存、抓取和操作的物件。</span><span class="sxs-lookup"><span data-stu-id="232ea-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="232ea-118">它會公開三個高階作業：</span><span class="sxs-lookup"><span data-stu-id="232ea-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="232ea-119">建立新的金鑰，並將它保存在儲存體中。</span><span class="sxs-lookup"><span data-stu-id="232ea-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="232ea-120">取得儲存體中的所有金鑰。</span><span class="sxs-lookup"><span data-stu-id="232ea-120">Get all keys from storage.</span></span>

* <span data-ttu-id="232ea-121">撤銷一或多個金鑰，並將撤銷資訊保存到儲存體。</span><span class="sxs-lookup"><span data-stu-id="232ea-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="232ea-122">撰寫 `IKeyManager` 是非常先進的工作，大部分的開發人員都不應該嘗試它。</span><span class="sxs-lookup"><span data-stu-id="232ea-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="232ea-123">相反地，大部分的開發人員都應該利用 [XmlKeyManager](#xmlkeymanager) 類別所提供的功能。</span><span class="sxs-lookup"><span data-stu-id="232ea-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="232ea-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="232ea-124">XmlKeyManager</span></span>

<span data-ttu-id="232ea-125">此 `XmlKeyManager` 類型是的內建實體實作為 `IKeyManager` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="232ea-126">它提供數個實用的工具，包括待用金鑰的金鑰加密和加密。</span><span class="sxs-lookup"><span data-stu-id="232ea-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="232ea-127">這個系統中的索引鍵會以 XML 專案的形式呈現， (明確 [system.xml.linq.xelement>](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) 。</span><span class="sxs-lookup"><span data-stu-id="232ea-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="232ea-128">`XmlKeyManager` 在完成其工作的過程中，相依于其他幾個元件：</span><span class="sxs-lookup"><span data-stu-id="232ea-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="232ea-129">`AlgorithmConfiguration`，表示新索引鍵所使用的演算法。</span><span class="sxs-lookup"><span data-stu-id="232ea-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="232ea-130">`IXmlRepository`，可控制在儲存體中保存金鑰的位置。</span><span class="sxs-lookup"><span data-stu-id="232ea-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="232ea-131">`IXmlEncryptor` [選用]，這可讓您加密待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="232ea-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="232ea-132">`IKeyEscrowSink` [選用]，提供金鑰的可提供服務。</span><span class="sxs-lookup"><span data-stu-id="232ea-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="232ea-133">`IXmlRepository`，可控制在儲存體中保存金鑰的位置。</span><span class="sxs-lookup"><span data-stu-id="232ea-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="232ea-134">`IXmlEncryptor` [選用]，這可讓您加密待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="232ea-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="232ea-135">`IKeyEscrowSink` [選用]，提供金鑰的可提供服務。</span><span class="sxs-lookup"><span data-stu-id="232ea-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="232ea-136">以下是高層級的圖表，指出這些元件如何一起連接在一起 `XmlKeyManager` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![建立金鑰](key-management/_static/keycreation2.png)

<span data-ttu-id="232ea-138">*金鑰建立/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="232ea-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="232ea-139">在的執行中 `CreateNewKey` ， `AlgorithmConfiguration` 元件是用來建立唯一的 `IAuthenticatedEncryptorDescriptor` ，然後再序列化為 XML。</span><span class="sxs-lookup"><span data-stu-id="232ea-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="232ea-140">如果有金鑰內建接收， (則會將未經加密的未經加密) XML 提供給接收以進行長期儲存。</span><span class="sxs-lookup"><span data-stu-id="232ea-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="232ea-141">`IXmlEncryptor`如果需要) 產生加密的 xml 檔，則未加密的 xml 接著會透過 (執行。</span><span class="sxs-lookup"><span data-stu-id="232ea-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="232ea-142">此加密檔會透過來保存到長期儲存 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="232ea-143"> (如果未 `IXmlEncryptor` 設定，則會將未加密的檔保存在中 `IXmlRepository` 。 ) </span><span class="sxs-lookup"><span data-stu-id="232ea-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![金鑰抓取](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![建立金鑰](key-management/_static/keycreation1.png)

<span data-ttu-id="232ea-146">*金鑰建立/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="232ea-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="232ea-147">在的執行中 `CreateNewKey` ， `IAuthenticatedEncryptorConfiguration` 元件是用來建立唯一的 `IAuthenticatedEncryptorDescriptor` ，然後再序列化為 XML。</span><span class="sxs-lookup"><span data-stu-id="232ea-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="232ea-148">如果有金鑰內建接收， (則會將未經加密的未經加密) XML 提供給接收以進行長期儲存。</span><span class="sxs-lookup"><span data-stu-id="232ea-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="232ea-149">`IXmlEncryptor`如果需要) 產生加密的 xml 檔，則未加密的 xml 接著會透過 (執行。</span><span class="sxs-lookup"><span data-stu-id="232ea-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="232ea-150">此加密檔會透過來保存到長期儲存 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="232ea-151"> (如果未 `IXmlEncryptor` 設定，則會將未加密的檔保存在中 `IXmlRepository` 。 ) </span><span class="sxs-lookup"><span data-stu-id="232ea-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![金鑰抓取](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="232ea-153">*金鑰抓取/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="232ea-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="232ea-154">在的執行中 `GetAllKeys` ，代表索引鍵和撤銷的 XML 檔是從基礎讀取的 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="232ea-155">如果這些檔經過加密，系統會自動將它們解密。</span><span class="sxs-lookup"><span data-stu-id="232ea-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="232ea-156">`XmlKeyManager` 建立適當的 `IAuthenticatedEncryptorDescriptorDeserializer` 實例，以將檔案還原序列化回 `IAuthenticatedEncryptorDescriptor` 實例，然後包裝在個別 `IKey` 實例中。</span><span class="sxs-lookup"><span data-stu-id="232ea-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="232ea-157">此實例集合 `IKey` 會傳回給呼叫端。</span><span class="sxs-lookup"><span data-stu-id="232ea-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="232ea-158">如需特定 XML 元素的詳細資訊，請參閱 [金鑰儲存格式檔](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)。</span><span class="sxs-lookup"><span data-stu-id="232ea-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="232ea-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-159">IXmlRepository</span></span>

<span data-ttu-id="232ea-160">`IXmlRepository`介面代表可保存 xml 的型別，並從備份存放區取出 xml。</span><span class="sxs-lookup"><span data-stu-id="232ea-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="232ea-161">它會公開兩個 Api：</span><span class="sxs-lookup"><span data-stu-id="232ea-161">It exposes two APIs:</span></span>

* <span data-ttu-id="232ea-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="232ea-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="232ea-163">的 `IXmlRepository` 執行不需要剖析傳遞的 XML。</span><span class="sxs-lookup"><span data-stu-id="232ea-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="232ea-164">它們應該會將 XML 檔視為不透明，並讓較高層的人員擔心產生和剖析檔。</span><span class="sxs-lookup"><span data-stu-id="232ea-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="232ea-165">有四個內建的實體型別，這些型別會執行 `IXmlRepository` ：</span><span class="sxs-lookup"><span data-stu-id="232ea-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="232ea-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="232ea-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="232ea-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="232ea-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="232ea-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="232ea-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="232ea-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="232ea-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="232ea-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="232ea-174">如需詳細資訊，請參閱 [金鑰儲存提供者檔](xref:security/data-protection/implementation/key-storage-providers) 。</span><span class="sxs-lookup"><span data-stu-id="232ea-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="232ea-175">`IXmlRepository`使用不同的備份存放區時，註冊自訂是適當的 (例如 Azure 資料表儲存體) 。</span><span class="sxs-lookup"><span data-stu-id="232ea-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="232ea-176">若要變更預設儲存機制的整個應用程式，請註冊自訂 `IXmlRepository` 實例：</span><span class="sxs-lookup"><span data-stu-id="232ea-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="232ea-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="232ea-177">IXmlEncryptor</span></span>

<span data-ttu-id="232ea-178">`IXmlEncryptor`介面代表可以加密純文字 XML 專案的型別。</span><span class="sxs-lookup"><span data-stu-id="232ea-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="232ea-179">它會公開單一 API：</span><span class="sxs-lookup"><span data-stu-id="232ea-179">It exposes a single API:</span></span>

* <span data-ttu-id="232ea-180">加密 (System.xml.linq.xelement> plaiNtextElement) ： EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="232ea-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="232ea-181">如果序列化 `IAuthenticatedEncryptorDescriptor` 包含任何標記為「需要加密」的專案，則 `XmlKeyManager` 會透過設定的方法執行這些 `IXmlEncryptor` 元素 `Encrypt` ，並將 enciphered 專案（而非純文字元素）保存到 `IXmlRepository` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="232ea-182">方法的輸出 `Encrypt` 是 `EncryptedXmlInfo` 物件。</span><span class="sxs-lookup"><span data-stu-id="232ea-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="232ea-183">此物件是包含結果 enciphered 的包裝函 `XElement` 式，以及表示 `IXmlDecryptor` 可用來解密對應元素的類型。</span><span class="sxs-lookup"><span data-stu-id="232ea-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="232ea-184">有四個內建的實體型別，這些型別會執行 `IXmlEncryptor` ：</span><span class="sxs-lookup"><span data-stu-id="232ea-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="232ea-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="232ea-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="232ea-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="232ea-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="232ea-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="232ea-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="232ea-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="232ea-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="232ea-189">如需詳細資訊，請參閱「 [靜態加密」檔](xref:security/data-protection/implementation/key-encryption-at-rest) 。</span><span class="sxs-lookup"><span data-stu-id="232ea-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="232ea-190">若要變更整個應用程式的預設金鑰靜態加密機制，請註冊自訂 `IXmlEncryptor` 實例：</span><span class="sxs-lookup"><span data-stu-id="232ea-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="232ea-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="232ea-191">IXmlDecryptor</span></span>

<span data-ttu-id="232ea-192">`IXmlDecryptor`介面代表一種型別，它知道如何解密透過所 `XElement` enciphered 的 `IXmlEncryptor` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="232ea-193">它會公開單一 API：</span><span class="sxs-lookup"><span data-stu-id="232ea-193">It exposes a single API:</span></span>

* <span data-ttu-id="232ea-194">解密 (System.xml.linq.xelement> encryptedElement) ： System.xml.linq.xelement></span><span class="sxs-lookup"><span data-stu-id="232ea-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="232ea-195">`Decrypt`方法會復原執行的加密 `IXmlEncryptor.Encrypt` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="232ea-196">一般來說，每個具體 `IXmlEncryptor` 的執行都會有對應的具體 `IXmlDecryptor` 實作為。</span><span class="sxs-lookup"><span data-stu-id="232ea-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="232ea-197">要執行的型別 `IXmlDecryptor` 應該具有下列兩個公用函式的其中一個：</span><span class="sxs-lookup"><span data-stu-id="232ea-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="232ea-198">.ctor (IServiceProvider) </span><span class="sxs-lookup"><span data-stu-id="232ea-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="232ea-199">.ctor ( # A1</span><span class="sxs-lookup"><span data-stu-id="232ea-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="232ea-200">`IServiceProvider`傳遞給此函式的會是 null。</span><span class="sxs-lookup"><span data-stu-id="232ea-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="232ea-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="232ea-201">IKeyEscrowSink</span></span>

<span data-ttu-id="232ea-202">`IKeyEscrowSink`介面代表可執行機密資訊的型別。</span><span class="sxs-lookup"><span data-stu-id="232ea-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="232ea-203">回想一下，序列化描述項可能包含機密資訊 (例如加密內容) ，而這就是首次引進 [IXmlEncryptor](#ixmlencryptor) 型別的結果。</span><span class="sxs-lookup"><span data-stu-id="232ea-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="232ea-204">不過，發生意外狀況，而且可以刪除或損毀金鑰環形。</span><span class="sxs-lookup"><span data-stu-id="232ea-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="232ea-205">此證書處理介面提供了緊急的換用程式影線，可在任何已設定的 [IXmlEncryptor](#ixmlencryptor)轉換之前，允許存取原始序列化的 XML。</span><span class="sxs-lookup"><span data-stu-id="232ea-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="232ea-206">介面會公開單一 API：</span><span class="sxs-lookup"><span data-stu-id="232ea-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="232ea-207">儲存 (Guid keyId，System.xml.linq.xelement> 元素) </span><span class="sxs-lookup"><span data-stu-id="232ea-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="232ea-208">它是由執行， `IKeyEscrowSink` 以安全的方式處理提供的專案，與商務原則一致。</span><span class="sxs-lookup"><span data-stu-id="232ea-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="232ea-209">其中一個可能的執行可能是，使用已知公司的 x.509 憑證來加密 XML 專案，而該憑證已委付憑證的私密金鑰; `CertificateXmlEncryptor` 此類型可以協助您。</span><span class="sxs-lookup"><span data-stu-id="232ea-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="232ea-210">此 `IKeyEscrowSink` 實也會負責適當保存提供的元素。</span><span class="sxs-lookup"><span data-stu-id="232ea-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="232ea-211">依預設，不會啟用任何的託管機制，但是伺服器管理員可以進行 [全域設定](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="232ea-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="232ea-212">它也可以透過方法以程式設計方式來設定， `IDataProtectionBuilder.AddKeyEscrowSink` 如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="232ea-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="232ea-213">方法多載會 `AddKeyEscrowSink` 鏡像 `IServiceCollection.AddSingleton` 和多載 `IServiceCollection.AddInstance` ，因為 `IKeyEscrowSink` 實例應 singleton。</span><span class="sxs-lookup"><span data-stu-id="232ea-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="232ea-214">如果 `IKeyEscrowSink` 註冊了多個實例，每個實例都會在金鑰產生期間呼叫，因此可以同時將金鑰委付至多個機制。</span><span class="sxs-lookup"><span data-stu-id="232ea-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="232ea-215">沒有 API 可從實例讀取資料 `IKeyEscrowSink` 。</span><span class="sxs-lookup"><span data-stu-id="232ea-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="232ea-216">這與授權機制的設計理論一致：它的目的是要讓受信任的授權單位可以存取金鑰內容，而且因為應用程式本身不是受信任的授權單位，所以它不應該有自己的委付資料存取權。</span><span class="sxs-lookup"><span data-stu-id="232ea-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="232ea-217">下列範例程式碼示範如何建立和註冊 `IKeyEscrowSink` where 索引鍵的委付，如此一來，只有 "CONTOSODomain Admins" 的成員才能復原它們。</span><span class="sxs-lookup"><span data-stu-id="232ea-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="232ea-218">若要執行此範例，您必須在已加入網域的 Windows 8/Windows Server 2012 電腦上，而且網域控制站必須是 Windows Server 2012 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="232ea-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
