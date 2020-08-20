---
title: ASP.NET Core 中的金鑰儲存格式
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護金鑰儲存格式的執行詳細資料。
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631988"
---
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET Core 中的金鑰儲存格式

<a name="data-protection-implementation-key-storage-format"></a>

物件會以 XML 標記法儲存在靜止的位置。 金鑰存放區的預設目錄為：

* Windows： *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux： *$HOME/.aspnet/dataprotection-keys*

## <a name="the-key-element"></a>\<key> 項目

金鑰會以最上層物件的形式存在於金鑰儲存機制中。 依照慣例，索引鍵的檔案名為 **{guid} .xml**，其中 {guid} 是金鑰的識別碼。 每個這類檔案都包含單一金鑰。 檔案的格式如下所示。

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

\<key>元素包含下列屬性和子項目：

* 金鑰識別碼。此值被視為授權;檔案名只是人們可讀性的 nicety。

* 專案的版本 \<key> ，目前已從1修正。

* 金鑰的建立、啟用和到期日。

* \<descriptor>元素，其中包含此金鑰內所包含之已驗證加密執行的資訊。

在上述範例中，索引鍵的識別碼是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它是在2015年3月19日建立和啟用，而且存留期為90天。  (偶爾的啟用日期可能會在建立日期之前稍微稍微早于此範例中。 這是因為 Api 的運作方式 nit，在實務上是無害的。 ) 

## <a name="the-descriptor-element"></a>\<descriptor> 項目

Outer \<descriptor> 元素包含屬性 deserializerType，這是實 IAuthenticatedEncryptorDescriptorDeserializer 之型別的元件限定名稱。 此類型負責讀取內部 \<descriptor> 元素，以及剖析包含在內的資訊。

專案的特定格式 \<descriptor> 取決於金鑰所封裝的經過驗證的加密程式執行，而且每個還原序列化程式的類型都必須有稍微不同的格式。 但一般而言，此元素將包含演算法資訊 (名稱、類型、Oid 或類似的) 和秘密金鑰內容。 在上述範例中，描述項指定此金鑰會包裝 AES-256-CBC 加密 + HMACSHA256 驗證。

## <a name="the-encryptedsecret-element"></a>\<encryptedSecret> 項目

如果[已啟用待用秘密的加密，](xref:security/data-protection/implementation/key-encryption-at-rest)則包含加密形式之秘密金鑰內容的** &lt; encryptedSecret &gt; **元素可能會存在。 屬性 `decryptorType` 是實 [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)之型別的元件限定名稱。 此類型負責讀取內部** &lt; encryptedKey &gt; **元素，並將其解密以復原原始純文字。

如同 `<descriptor>` ，元素的特定格式取決於 `<encryptedSecret>` 使用中的靜止加密機制。 在上述範例中，主要金鑰會根據批註使用 Windows DPAPI 進行加密。

## <a name="the-revocation-element"></a>\<revocation> 項目

撤銷會以最上層物件的形式存在於金鑰儲存機制中。 依照慣例，撤銷具有 filename **撤銷-{timestamp} .xml** (，可在特定日期) 或 **撤銷-{guid} .xml** (撤銷特定的金鑰) 之前撤銷所有金鑰。 每個檔案都包含單一 \<revocation> 元素。

針對個別索引鍵的撤銷，檔案內容將如下所示。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

在此情況下，只會撤銷指定的金鑰。 但是，如果金鑰識別碼為 "*"，如下列範例所示，則會撤銷其建立日期早于指定撤銷日期的所有金鑰。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<reason>系統永遠不會讀取元素。 這只是一個方便的位置，可儲存人類可讀取的撤銷原因。
