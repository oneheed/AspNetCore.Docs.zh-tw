---
title: ASP.NET Core 中的核心加密擴充性
author: rick-anderson
description: 瞭解 IAuthenticatedEncryptor、IAuthenticatedEncryptorDescriptor、IAuthenticatedEncryptorDescriptorDeserializer 和最上層 factory。
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 4c802bc4beb1f1fde812e6c3f55fc43b5d569b66
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635316"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>ASP.NET Core 中的核心加密擴充性

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> 實作為下列任何介面的型別，都應該是多個呼叫端的安全線程。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**IAuthenticatedEncryptor**介面是密碼編譯子系統的基本組建區塊。 一般來說，每個金鑰都有一個 IAuthenticatedEncryptor，而 IAuthenticatedEncryptor 實例會包裝執行密碼編譯作業所需的所有密碼編譯金鑰內容和演算法資訊。

顧名思義，型別負責提供經過驗證的加密和解密服務。 它會公開下列兩個 Api。

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Encrypt 方法會傳回包含 enciphered 純文字和驗證標記的 blob。 驗證標記必須包含額外的已驗證資料 (AAD) ，不過 AAD 本身不需要從最終承載復原。 解密方法會驗證驗證標記，並傳回解密的裝載。 除了 System.argumentnullexception 和類似的) 以外的所有失敗 (都應該 homogenized 到 System.security.cryptography.cryptographicexception。

> [!NOTE]
> IAuthenticatedEncryptor 實例本身實際上不需要包含金鑰內容。 例如，執行可能會將所有作業的 HSM 委派給 HSM。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>如何建立 IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorFactory**介面代表知道如何建立[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)實例的型別。 其 API 如下所示。

* CreateEncryptorInstance (IKey key) ： IAuthenticatedEncryptor

針對任何指定的 IKey 實例，其 CreateEncryptorInstance 方法所建立的任何已驗證加密程式都應視為相同，如下列程式碼範例所示。

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorDescriptor**介面代表知道如何建立[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)實例的型別。 其 API 如下所示。

* CreateEncryptorInstance ( # A1： IAuthenticatedEncryptor

* ExportToXml ( # A1： XmlSerializedDescriptorInfo

如同 IAuthenticatedEncryptor，系統會假設 IAuthenticatedEncryptorDescriptor 的實例包裝一個特定的索引鍵。 這表示，針對任何指定的 IAuthenticatedEncryptorDescriptor 實例，其 CreateEncryptorInstance 方法所建立的任何已驗證加密程式都應視為相同，如下列程式碼範例所示。

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (只 ASP.NET Core 2.x) 

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorDescriptor**介面代表一種知道如何將本身匯出為 XML 的型別。 其 API 如下所示。

* ExportToXml ( # A1： XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML 序列化

IAuthenticatedEncryptor 和 IAuthenticatedEncryptorDescriptor 之間的主要差異在於描述元知道如何建立加密程式，並提供有效的引數給它。 請考慮採用 SymmetricAlgorithm 和 KeyedHashAlgorithm 的 IAuthenticatedEncryptor。 加密程式的工作是使用這些類型，但不一定知道這些型別來自何處，因此它無法真的寫出適當的描述，說明如何在應用程式重新開機時自行重新建立。 描述項會以較高的層級作為其最上層。 因為描述元知道如何建立加密程式實例 (例如，它知道如何建立所需的演算法) ，它可以將該知識序列化為 XML 格式，以便在應用程式重設之後重新建立加密程式實例。

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

描述元可以透過其 ExportToXml 常式進行序列化。 這個常式會傳回包含兩個屬性的 XmlSerializedDescriptorInfo：描述項的 System.xml.linq.xelement> 標記法，以及代表 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) 的型別，該型別可以用來在給定對應的 system.xml.linq.xelement> resurrect 這個描述項。

序列化描述項可能包含機密資訊，例如密碼編譯金鑰內容。 在保存到儲存體之前，資料保護系統內建了加密資訊的支援。 若要利用這種方式，描述項應將包含敏感性資訊的元素標示為 "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> " ) ，值 "true"。

>[!TIP]
> 有一個協助程式 API 可設定這個屬性。 呼叫位於命名空間 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 中的擴充方法 System.xml.linq.xelement>. MarkAsRequiresEncryption ( # A1。

此外，也可能會有序列化描述元未包含機密資訊的情況。 請再次考慮儲存在 HSM 中的密碼編譯金鑰大小寫。 當您序列化時，描述項無法寫出金鑰內容，因為 HSM 不會以純文字形式公開材質。 相反地，如果 HSM 以這種方式進行匯出，則描述元可能會寫出金鑰包裝版 (，) 或 HSM 本身的唯一識別碼。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IAuthenticatedEncryptorDescriptorDeserializer**介面代表一種類型，知道如何從 system.xml.linq.xelement> 還原序列化 IAuthenticatedEncryptorDescriptor 實例。 它會公開單一方法：

* ImportFromXml (System.xml.linq.xelement> 元素) ： IAuthenticatedEncryptorDescriptor

ImportFromXml 方法會採用 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) 所傳回的 system.xml.linq.xelement>，並建立對等的原始 IAuthenticatedEncryptorDescriptor。

實 IAuthenticatedEncryptorDescriptorDeserializer 的類型應該有下列兩個公用函式的其中一個：

* .ctor (IServiceProvider) 

* .ctor ( # A1

> [!NOTE]
> 傳遞給此函式的 IServiceProvider 可能是 null。

## <a name="the-top-level-factory"></a>最上層 factory

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**AlgorithmConfiguration**類別代表知道如何建立[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)實例的型別。 它會公開單一 API。

* CreateNewDescriptor ( # A1： IAuthenticatedEncryptorDescriptor

將 AlgorithmConfiguration 視為最上層 factory。 設定會作為範本。 它會包裝演算法資訊 (例如，此設定會產生具有 AES-128-GCM 主要金鑰) 的描述元，但尚未與特定金鑰相關聯。

呼叫 CreateNewDescriptor 時，只會針對此呼叫建立新的金鑰內容，並產生新的 IAuthenticatedEncryptorDescriptor，以包裝此金鑰內容和取用材質所需的演算法資訊。 您可以在軟體 (中建立金鑰內容，並將它保存在記憶體) ，它可以在 HSM 內建立和保留，依此類推。 重點是，任何兩次對 CreateNewDescriptor 的呼叫都不應該建立對等的 IAuthenticatedEncryptorDescriptor 實例。

AlgorithmConfiguration 型別可做為金鑰建立常式的進入點，例如 [自動金鑰滾動](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)。 若要變更所有未來索引鍵的執行，請在 KeyManagementOptions 中設定 AuthenticatedEncryptorConfiguration 屬性。

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorConfiguration**介面代表知道如何建立[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)實例的型別。 它會公開單一 API。

* CreateNewDescriptor ( # A1： IAuthenticatedEncryptorDescriptor

將 IAuthenticatedEncryptorConfiguration 視為最上層 factory。 設定會作為範本。 它會包裝演算法資訊 (例如，此設定會產生具有 AES-128-GCM 主要金鑰) 的描述元，但尚未與特定金鑰相關聯。

呼叫 CreateNewDescriptor 時，只會針對此呼叫建立新的金鑰內容，並產生新的 IAuthenticatedEncryptorDescriptor，以包裝此金鑰內容和取用材質所需的演算法資訊。 您可以在軟體 (中建立金鑰內容，並將它保存在記憶體) ，它可以在 HSM 內建立和保留，依此類推。 重點是，任何兩次對 CreateNewDescriptor 的呼叫都不應該建立對等的 IAuthenticatedEncryptorDescriptor 實例。

IAuthenticatedEncryptorConfiguration 型別可做為金鑰建立常式的進入點，例如 [自動金鑰滾動](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)。 若要變更所有未來金鑰的執行，請在服務容器中註冊單一 IAuthenticatedEncryptorConfiguration。

---
