---
title: ASP.NET Core 中的子機碼衍生和驗證加密
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護子機碼衍生和驗證加密的執行詳細資料。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: efe8ad2f71feda9cbc1693d362e30eff29cbcd74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060153"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>ASP.NET Core 中的子機碼衍生和驗證加密

<a name="data-protection-implementation-subkey-derivation"></a>

金鑰通道中的大部分金鑰都會包含某種形式的熵，而且會有說明「CBC 模式加密 + HMAC 驗證」或「GCM 加密 + 驗證」的演算法資訊。 在這些情況下，我們會將內嵌的熵稱為此金鑰的主要金鑰處理 (或公里) ，我們會執行金鑰衍生函式，以衍生將用於實際密碼編譯作業的金鑰。

> [!NOTE]
> 索引鍵為抽象，自訂執行的行為可能不會如下所示。 如果金鑰提供自己的執行 `IAuthenticatedEncryptor` ，而不是使用其中一個內建 factory，則本節所述的機制不再適用。

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>其他已驗證的資料和子機碼衍生

`IAuthenticatedEncryptor`介面會作為所有已驗證加密作業的核心介面。 其 `Encrypt` 方法採用兩個緩衝區：純文字和 additionalAuthenticatedData (AAD) 。 純文字內容的流程未變更為 `IDataProtector.Protect` ，但 AAD 是由系統產生，並且包含三個元件：

1. 指出此版本資料保護系統的32位魔術標頭 09 F0 C9 F0。

2. 128位金鑰識別碼。

3. 從建立執行此作業之的目的鏈所形成的可變長度字串 `IDataProtector` 。

由於 AAD 對於這三個元件的元組而言是唯一的，因此我們可以使用它來從公里衍生新的金鑰，而不是在我們的所有密碼編譯作業中使用公里本身。 每次呼叫時 `IAuthenticatedEncryptor.Encrypt` ，都會發生下列金鑰衍生程式：

`( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)`

在此，我們會在計數器模式中呼叫 NIST SP800-108 KDF (查看 [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，Sec. 5.1) 具有下列參數：

* 金鑰衍生金鑰 (KDK) = `K_M`

* PRF = HMACSHA512

* 標籤 = additionalAuthenticatedData

* coNtext = coNtextHeader | |keyModifier

內容標頭的長度是可變的，基本上是作為我們所衍生的演算法的指紋 `K_E` `K_H` 。 Key 修飾詞是針對的每個呼叫隨機產生的128位字串 `Encrypt` ，可確保在此特定驗證加密作業中，KE 和 KH 是唯一的機率，即使 KDF 的所有其他輸入都是常數。

在 CBC 模式加密 + HMAC 驗證作業中， `| K_E |` 是對稱區塊加密金鑰的長度，而且 `| K_H |` 是 HMAC 常式的摘要大小。 適用于 GCM 加密 + 驗證作業 `| K_H | = 0` 。

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC 模式加密 + HMAC 驗證

一旦透過 `K_E` 上述機制產生，我們會產生隨機的初始化向量並執行對稱封鎖密碼演算法，以加密純文字。 初始化向量和加密文字接著會透過以金鑰初始化的 HMAC 常式來執行， `K_H` 以產生 MAC。 此進程和傳回值以圖形方式呈現。

![CBC 模式進程和傳回](subkeyderivation/_static/cbcprocess.png)

`output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))`

> [!NOTE]
> 在 `IDataProtector.Protect` 將 [魔術標頭和金鑰識別碼](xref:security/data-protection/implementation/authenticated-encryption-details) 傳回給呼叫者之前，會先在其上加上此實作為輸出。 由於魔術標頭和金鑰識別碼是 [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)的一部分，而且因為金鑰修飾詞會以輸入的形式饋送至 KDF，這表示最後傳回的承載的每個位元組都是由 MAC 進行驗證。

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/計數器模式加密 + 驗證

一旦透過 `K_E` 上述機制產生，我們會產生隨機的96位 nonce 並執行對稱封鎖密碼演算法，以加密純文字並產生128位驗證標記。

![GCM 模式的進程和退貨](subkeyderivation/_static/galoisprocess.png)

`output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag`

> [!NOTE]
> 雖然 GCM 原生支援 AAD 的概念，但我們還是只會將 AAD 饋送至原始 KDF，選擇將空字串傳遞至 GCM 以取得其 AAD 參數。 這是兩個折迭的原因。 首先， [為了支援靈活性](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) ，我們絕對不想要 `K_M` 直接使用加密金鑰。 此外，GCM 對其輸入強加嚴格的唯一性需求。 在兩個或多個不同的輸入資料集上，使用相同的 (機碼來叫用 GCM 加密常式的機率，nonce) 組不得超過 2 ^ 32。 如果我們修正了我們在執行 `K_E` 2 ^-32 限制的 afoul 之前，無法執行超過 2 ^ 32 個加密作業。 這看起來可能像是非常大量的作業，但高流量的 web 伺服器可以只在這些金鑰的正常存留期內，在單純的天數內進行4000000000的要求。 為了維持符合 2 ^-32 機率限制的規範，我們會繼續使用128位金鑰修飾詞和96位 nonce，以大幅擴充任何給定的可用作業計數 `K_M` 。 為了簡化設計，我們會在 CBC 與 GCM 作業之間共用 KDF 程式碼路徑，而且因為 KDF 中已考慮 AAD，所以不需要將它轉寄到 GCM 常式。
