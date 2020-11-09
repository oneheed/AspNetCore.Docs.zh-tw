---
title: ASP.NET Core 中的金鑰儲存格式
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護金鑰儲存格式的執行詳細資料。
ms.author: riande
ms.date: 04/08/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060140"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="cc7f8-103">ASP.NET Core 中的金鑰儲存格式</span><span class="sxs-lookup"><span data-stu-id="cc7f8-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="cc7f8-104">物件會以 XML 標記法儲存在靜止的位置。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="cc7f8-105">金鑰存放區的預設目錄為：</span><span class="sxs-lookup"><span data-stu-id="cc7f8-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="cc7f8-106">Windows： \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="cc7f8-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="cc7f8-107">macOS/Linux： *$HOME/.aspnet/dataprotection-keys*</span><span class="sxs-lookup"><span data-stu-id="cc7f8-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="cc7f8-108">\<key> 項目</span><span class="sxs-lookup"><span data-stu-id="cc7f8-108">The \<key> element</span></span>

<span data-ttu-id="cc7f8-109">金鑰會以最上層物件的形式存在於金鑰儲存機制中。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="cc7f8-110">依照慣例，索引鍵的檔案名為 **{guid} .xml** ，其中 {guid} 是金鑰的識別碼。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="cc7f8-111">每個這類檔案都包含單一金鑰。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-111">Each such file contains a single key.</span></span> <span data-ttu-id="cc7f8-112">檔案的格式如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="cc7f8-113">\<key>元素包含下列屬性和子項目：</span><span class="sxs-lookup"><span data-stu-id="cc7f8-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="cc7f8-114">金鑰識別碼。此值被視為授權;檔案名只是人們可讀性的 nicety。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="cc7f8-115">專案的版本 \<key> ，目前已從1修正。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="cc7f8-116">金鑰的建立、啟用和到期日。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="cc7f8-117">\<descriptor>元素，其中包含此金鑰內所包含之已驗證加密執行的資訊。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="cc7f8-118">在上述範例中，索引鍵的識別碼是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它是在2015年3月19日建立和啟用，而且存留期為90天。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="cc7f8-119"> (偶爾的啟用日期可能會在建立日期之前稍微稍微早于此範例中。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="cc7f8-120">這是因為 Api 的運作方式 nit，在實務上是無害的。 ) </span><span class="sxs-lookup"><span data-stu-id="cc7f8-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="cc7f8-121">\<descriptor> 項目</span><span class="sxs-lookup"><span data-stu-id="cc7f8-121">The \<descriptor> element</span></span>

<span data-ttu-id="cc7f8-122">Outer \<descriptor> 元素包含屬性 deserializerType，這是實 IAuthenticatedEncryptorDescriptorDeserializer 之型別的元件限定名稱。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="cc7f8-123">此類型負責讀取內部 \<descriptor> 元素，以及剖析包含在內的資訊。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="cc7f8-124">專案的特定格式 \<descriptor> 取決於金鑰所封裝的經過驗證的加密程式執行，而且每個還原序列化程式的類型都必須有稍微不同的格式。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="cc7f8-125">但一般而言，此元素將包含演算法資訊 (名稱、類型、Oid 或類似的) 和秘密金鑰內容。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="cc7f8-126">在上述範例中，描述項指定此金鑰會包裝 AES-256-CBC 加密 + HMACSHA256 驗證。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="cc7f8-127">\<encryptedSecret> 項目</span><span class="sxs-lookup"><span data-stu-id="cc7f8-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="cc7f8-128">如果 [已啟用待用秘密的加密，](xref:security/data-protection/implementation/key-encryption-at-rest)則包含加密形式之秘密金鑰內容的 **&lt; encryptedSecret &gt;** 元素可能會存在。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="cc7f8-129">屬性 `decryptorType` 是實 [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)之型別的元件限定名稱。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="cc7f8-130">此類型負責讀取內部 **&lt; encryptedKey &gt;** 元素，並將其解密以復原原始純文字。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="cc7f8-131">如同 `<descriptor>` ，元素的特定格式取決於 `<encryptedSecret>` 使用中的靜止加密機制。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="cc7f8-132">在上述範例中，主要金鑰會根據批註使用 Windows DPAPI 進行加密。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="cc7f8-133">\<revocation> 項目</span><span class="sxs-lookup"><span data-stu-id="cc7f8-133">The \<revocation> element</span></span>

<span data-ttu-id="cc7f8-134">撤銷會以最上層物件的形式存在於金鑰儲存機制中。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="cc7f8-135">依照慣例，撤銷具有 filename **撤銷-{timestamp} .xml** (，可在特定日期) 或 **撤銷-{guid} .xml** (撤銷特定的金鑰) 之前撤銷所有金鑰。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="cc7f8-136">每個檔案都包含單一 \<revocation> 元素。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="cc7f8-137">針對個別索引鍵的撤銷，檔案內容將如下所示。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="cc7f8-138">在此情況下，只會撤銷指定的金鑰。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="cc7f8-139">但是，如果金鑰識別碼為 "\*"，如下列範例所示，則會撤銷其建立日期早于指定撤銷日期的所有金鑰。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="cc7f8-140">\<reason>系統永遠不會讀取元素。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="cc7f8-141">這只是一個方便的位置，可儲存人類可讀取的撤銷原因。</span><span class="sxs-lookup"><span data-stu-id="cc7f8-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
