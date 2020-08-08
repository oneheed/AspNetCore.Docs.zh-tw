---
title: ASP.NET Core 中的核心密碼編譯擴充性
author: rick-anderson
description: 瞭解 IAuthenticatedEncryptor、IAuthenticatedEncryptorDescriptor、IAuthenticatedEncryptorDescriptorDeserializer 和最上層的 factory。
ms.author: riande
ms.date: 08/11/2017
no-loc:
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
ms.openlocfilehash: 321e039a4b4692266c6d7ceaff96578db8a74bb5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021597"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="86094-103">ASP.NET Core 中的核心密碼編譯擴充性</span><span class="sxs-lookup"><span data-stu-id="86094-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="86094-104">實作為下列任何介面的型別應該是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="86094-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="86094-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="86094-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="86094-106">**IAuthenticatedEncryptor**介面是密碼編譯子系統的基本建立區塊。</span><span class="sxs-lookup"><span data-stu-id="86094-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="86094-107">一般來說，每個索引鍵都有一個 IAuthenticatedEncryptor，而 IAuthenticatedEncryptor 實例會包裝執行密碼編譯作業所需的所有密碼編譯金鑰內容和演算法資訊。</span><span class="sxs-lookup"><span data-stu-id="86094-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="86094-108">如其名所示，類型會負責提供已驗證的加密和解密服務。</span><span class="sxs-lookup"><span data-stu-id="86094-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="86094-109">它會公開下列兩個 Api。</span><span class="sxs-lookup"><span data-stu-id="86094-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="86094-110">Encrypt 方法會傳回包含 enciphered 純文字和驗證標記的 blob。</span><span class="sxs-lookup"><span data-stu-id="86094-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="86094-111">驗證標記必須包含額外的已驗證資料 (AAD) ，不過 AAD 本身不需要從最終承載復原。</span><span class="sxs-lookup"><span data-stu-id="86094-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="86094-112">解密方法會驗證驗證標記，並傳回解密的承載。</span><span class="sxs-lookup"><span data-stu-id="86094-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="86094-113">除了 System.argumentnullexception 和類似的) 以外的所有失敗 (都應該 homogenized 至 System.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="86094-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="86094-114">IAuthenticatedEncryptor 實例本身實際上不需要包含金鑰內容。</span><span class="sxs-lookup"><span data-stu-id="86094-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="86094-115">例如，執行可以委派給 HSM 進行所有作業。</span><span class="sxs-lookup"><span data-stu-id="86094-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="86094-116">如何建立 IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="86094-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="86094-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="86094-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="86094-118">**IAuthenticatedEncryptorFactory**介面代表知道如何建立[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)實例的類型。</span><span class="sxs-lookup"><span data-stu-id="86094-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="86094-119">其 API 如下所示。</span><span class="sxs-lookup"><span data-stu-id="86094-119">Its API is as follows.</span></span>

* <span data-ttu-id="86094-120">CreateEncryptorInstance (IKey 金鑰) ： IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="86094-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="86094-121">針對任何指定的 IKey 實例，其 CreateEncryptorInstance 方法所建立的任何已驗證 encryptors 都應該視為相同，如下列程式碼範例所示。</span><span class="sxs-lookup"><span data-stu-id="86094-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="86094-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="86094-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="86094-123">**IAuthenticatedEncryptorDescriptor**介面代表知道如何建立[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)實例的類型。</span><span class="sxs-lookup"><span data-stu-id="86094-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="86094-124">其 API 如下所示。</span><span class="sxs-lookup"><span data-stu-id="86094-124">Its API is as follows.</span></span>

* <span data-ttu-id="86094-125">CreateEncryptorInstance ( # A1： IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="86094-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="86094-126">ExportToXml ( # A1： XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="86094-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="86094-127">如同 IAuthenticatedEncryptor，系統會假設 IAuthenticatedEncryptorDescriptor 的實例包裝一個特定的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="86094-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="86094-128">這表示針對任何指定的 IAuthenticatedEncryptorDescriptor 實例，其 CreateEncryptorInstance 方法所建立的任何已驗證 encryptors 都應該視為相等，如下列程式碼範例所示。</span><span class="sxs-lookup"><span data-stu-id="86094-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="86094-129">僅限 IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x) </span><span class="sxs-lookup"><span data-stu-id="86094-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="86094-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="86094-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="86094-131">**IAuthenticatedEncryptorDescriptor**介面代表的類型，知道如何將其本身匯出至 XML。</span><span class="sxs-lookup"><span data-stu-id="86094-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="86094-132">其 API 如下所示。</span><span class="sxs-lookup"><span data-stu-id="86094-132">Its API is as follows.</span></span>

* <span data-ttu-id="86094-133">ExportToXml ( # A1： XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="86094-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="86094-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="86094-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="86094-135">XML 序列化</span><span class="sxs-lookup"><span data-stu-id="86094-135">XML Serialization</span></span>

<span data-ttu-id="86094-136">IAuthenticatedEncryptor 和 IAuthenticatedEncryptorDescriptor 之間的主要差異在於，描述項知道如何建立加密程式，並提供有效的引數給它。</span><span class="sxs-lookup"><span data-stu-id="86094-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="86094-137">請考慮採用 System.security.cryptography.symmetricalgorithm 和 KeyedHashAlgorithm 的 IAuthenticatedEncryptor。</span><span class="sxs-lookup"><span data-stu-id="86094-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="86094-138">加密程式的工作是使用這些類型，但不一定知道這些類型的來源，因此它無法真正寫出適當的描述，說明如何在應用程式重新開機時自行重新建立。</span><span class="sxs-lookup"><span data-stu-id="86094-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="86094-139">描述項在這個之上會當做較高層級。</span><span class="sxs-lookup"><span data-stu-id="86094-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="86094-140">由於描述項知道如何建立加密程式實例 (例如，它知道如何建立所需的演算法) ，它可以將該知識序列化成 XML 格式，以便在應用程式重設之後重新建立加密器實例。</span><span class="sxs-lookup"><span data-stu-id="86094-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="86094-141">描述元可以透過其 ExportToXml 常式進行序列化。</span><span class="sxs-lookup"><span data-stu-id="86094-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="86094-142">此常式會傳回 XmlSerializedDescriptorInfo，其中包含兩個屬性：描述元的 System.xml.linq.xelement> 標記法，以及代表[IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)的類型，可以用來 resurrect 此描述項（提供對應的 system.xml.linq.xelement>）。</span><span class="sxs-lookup"><span data-stu-id="86094-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="86094-143">序列化的描述項可能會包含機密資訊，例如密碼編譯金鑰內容。</span><span class="sxs-lookup"><span data-stu-id="86094-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="86094-144">資料保護系統在保存到儲存體之前，有內建的加密資訊支援。</span><span class="sxs-lookup"><span data-stu-id="86094-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="86094-145">若要利用這一點，描述項應該將包含具有屬性名稱 "requiresEncryption" 之機密資訊的元素標示為 "" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> " ) ，值 "true"。</span><span class="sxs-lookup"><span data-stu-id="86094-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="86094-146">有一個 helper API 可用於設定此屬性。</span><span class="sxs-lookup"><span data-stu-id="86094-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="86094-147">呼叫位於命名空間 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel ( # A1 的擴充方法 System.xml.linq.xelement>。</span><span class="sxs-lookup"><span data-stu-id="86094-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="86094-148">在某些情況下，序列化描述元不會包含敏感性資訊。</span><span class="sxs-lookup"><span data-stu-id="86094-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="86094-149">再次考慮在 HSM 中儲存密碼編譯金鑰的情況。</span><span class="sxs-lookup"><span data-stu-id="86094-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="86094-150">當序列化本身時，描述元無法寫出金鑰內容，因為 HSM 不會以純文字形式公開該內容。</span><span class="sxs-lookup"><span data-stu-id="86094-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="86094-151">取而代之的是，如果 HSM 允許以這種方式進行匯出，則描述項可能會寫出金鑰的金鑰包裝版本 () 或 HSM 本身唯一的金鑰識別碼。</span><span class="sxs-lookup"><span data-stu-id="86094-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="86094-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="86094-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="86094-153">**IAuthenticatedEncryptorDescriptorDeserializer**介面代表的類型，知道如何從 system.xml.linq.xelement> 還原序列化 IAuthenticatedEncryptorDescriptor 實例。</span><span class="sxs-lookup"><span data-stu-id="86094-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="86094-154">它會公開單一方法：</span><span class="sxs-lookup"><span data-stu-id="86094-154">It exposes a single method:</span></span>

* <span data-ttu-id="86094-155">ImportFromXml (System.xml.linq.xelement> 元素) ： IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="86094-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="86094-156">ImportFromXml 方法會採用[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)所傳回的 system.xml.linq.xelement>，並建立等同于原始 IAuthenticatedEncryptorDescriptor 的。</span><span class="sxs-lookup"><span data-stu-id="86094-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="86094-157">實 IAuthenticatedEncryptorDescriptorDeserializer 的類型應該有下列兩個公用函式的其中一個：</span><span class="sxs-lookup"><span data-stu-id="86094-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="86094-158"> (IServiceProvider) 的 .ctor</span><span class="sxs-lookup"><span data-stu-id="86094-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="86094-159">.ctor ( # A1</span><span class="sxs-lookup"><span data-stu-id="86094-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="86094-160">傳遞至此函式的 IServiceProvider 可能是 null。</span><span class="sxs-lookup"><span data-stu-id="86094-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="86094-161">最上層的 factory</span><span class="sxs-lookup"><span data-stu-id="86094-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="86094-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="86094-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="86094-163">**AlgorithmConfiguration**類別代表知道如何建立[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)實例的類型。</span><span class="sxs-lookup"><span data-stu-id="86094-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="86094-164">它會公開單一 API。</span><span class="sxs-lookup"><span data-stu-id="86094-164">It exposes a single API.</span></span>

* <span data-ttu-id="86094-165">CreateNewDescriptor ( # A1： IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="86094-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="86094-166">您想要將 AlgorithmConfiguration 視為最上層的 factory。</span><span class="sxs-lookup"><span data-stu-id="86094-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="86094-167">設定可作為範本。</span><span class="sxs-lookup"><span data-stu-id="86094-167">The configuration serves as a template.</span></span> <span data-ttu-id="86094-168">它會包裝演算法資訊 (例如，此設定會產生具有 AES-128-GCM 主要金鑰) 的描述元，但尚未與特定金鑰相關聯。</span><span class="sxs-lookup"><span data-stu-id="86094-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="86094-169">呼叫 CreateNewDescriptor 時，只會針對此呼叫建立新的金鑰材料，並產生會包裝此金鑰內容的新 IAuthenticatedEncryptorDescriptor，以及取用該資料所需的演算法資訊。</span><span class="sxs-lookup"><span data-stu-id="86094-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="86094-170">您可以在軟體 (中建立金鑰內容，並將其保留在記憶體中) 、可以在 HSM 內建立和保留，依此類推。</span><span class="sxs-lookup"><span data-stu-id="86094-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="86094-171">重點是，任何對 CreateNewDescriptor 的兩次呼叫都不應該建立對等的 IAuthenticatedEncryptorDescriptor 實例。</span><span class="sxs-lookup"><span data-stu-id="86094-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="86094-172">AlgorithmConfiguration 類型可做為金鑰建立常式的進入點，例如[自動金鑰回復](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)。</span><span class="sxs-lookup"><span data-stu-id="86094-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="86094-173">若要變更所有未來金鑰的執行，請在 KeyManagementOptions 中設定 AuthenticatedEncryptorConfiguration 屬性。</span><span class="sxs-lookup"><span data-stu-id="86094-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="86094-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="86094-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="86094-175">**IAuthenticatedEncryptorConfiguration**介面代表知道如何建立[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)實例的類型。</span><span class="sxs-lookup"><span data-stu-id="86094-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="86094-176">它會公開單一 API。</span><span class="sxs-lookup"><span data-stu-id="86094-176">It exposes a single API.</span></span>

* <span data-ttu-id="86094-177">CreateNewDescriptor ( # A1： IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="86094-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="86094-178">您想要將 IAuthenticatedEncryptorConfiguration 視為最上層的 factory。</span><span class="sxs-lookup"><span data-stu-id="86094-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="86094-179">設定可作為範本。</span><span class="sxs-lookup"><span data-stu-id="86094-179">The configuration serves as a template.</span></span> <span data-ttu-id="86094-180">它會包裝演算法資訊 (例如，此設定會產生具有 AES-128-GCM 主要金鑰) 的描述元，但尚未與特定金鑰相關聯。</span><span class="sxs-lookup"><span data-stu-id="86094-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="86094-181">呼叫 CreateNewDescriptor 時，只會針對此呼叫建立新的金鑰材料，並產生會包裝此金鑰內容的新 IAuthenticatedEncryptorDescriptor，以及取用該資料所需的演算法資訊。</span><span class="sxs-lookup"><span data-stu-id="86094-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="86094-182">您可以在軟體 (中建立金鑰內容，並將其保留在記憶體中) 、可以在 HSM 內建立和保留，依此類推。</span><span class="sxs-lookup"><span data-stu-id="86094-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="86094-183">重點是，任何對 CreateNewDescriptor 的兩次呼叫都不應該建立對等的 IAuthenticatedEncryptorDescriptor 實例。</span><span class="sxs-lookup"><span data-stu-id="86094-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="86094-184">IAuthenticatedEncryptorConfiguration 類型可做為金鑰建立常式的進入點，例如[自動金鑰回復](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)。</span><span class="sxs-lookup"><span data-stu-id="86094-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="86094-185">若要變更所有未來金鑰的執行，請在服務容器中註冊單一 IAuthenticatedEncryptorConfiguration。</span><span class="sxs-lookup"><span data-stu-id="86094-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
