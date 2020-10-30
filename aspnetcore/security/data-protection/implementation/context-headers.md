---
title: ASP.NET Core 中的內容標頭
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護內容標頭的執行詳細資料。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051716"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="0f387-103">ASP.NET Core 中的內容標頭</span><span class="sxs-lookup"><span data-stu-id="0f387-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="0f387-104">背景和理論</span><span class="sxs-lookup"><span data-stu-id="0f387-104">Background and theory</span></span>

<span data-ttu-id="0f387-105">在資料保護系統中，「金鑰」表示可提供已驗證加密服務的物件。</span><span class="sxs-lookup"><span data-stu-id="0f387-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="0f387-106">每個金鑰都是由 GUID)  (的唯一識別碼來識別，並隨附它的演算法資訊和 entropic 材質。</span><span class="sxs-lookup"><span data-stu-id="0f387-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="0f387-107">這是因為每個金鑰都有獨特的熵，但是系統無法強制執行這項工作，而且我們也需要將金鑰通道中現有金鑰的演算法資訊修改為手動變更金鑰環的開發人員。</span><span class="sxs-lookup"><span data-stu-id="0f387-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="0f387-108">為了達成我們的安全性需求，在這些情況下，資料保護系統具有 [密碼編譯靈活性](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)的概念，可讓您安全地在多個密碼編譯演算法中使用單一 entropic 值。</span><span class="sxs-lookup"><span data-stu-id="0f387-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="0f387-109">大部分支援密碼編譯靈活性的系統，都是在承載內包含有關演算法的一些識別資訊。</span><span class="sxs-lookup"><span data-stu-id="0f387-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="0f387-110">演算法的 OID 通常是很好的候選項。</span><span class="sxs-lookup"><span data-stu-id="0f387-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="0f387-111">不過，我們遇到的其中一個問題是，有多種方法可以指定相同的演算法：「AES」 (CNG) 和 managed Aes、AesManaged、AesCryptoServiceProvider、AesCng 和 RijndaelManaged (指定特定參數) 類別實際上是相同的，因此我們必須將所有這些都對應到正確的 OID。</span><span class="sxs-lookup"><span data-stu-id="0f387-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="0f387-112">如果開發人員想要提供自訂的演算法 (或甚至是 AES！ ) 的另一種執行，他們就必須告訴我們它的 OID。</span><span class="sxs-lookup"><span data-stu-id="0f387-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="0f387-113">這個額外的註冊步驟讓系統設定特別頭痛。</span><span class="sxs-lookup"><span data-stu-id="0f387-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="0f387-114">回頭回頭，我們決定我們從錯誤的方向中找出問題。</span><span class="sxs-lookup"><span data-stu-id="0f387-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="0f387-115">OID 會告訴您該演算法是什麼，但我們並不在意這一點。</span><span class="sxs-lookup"><span data-stu-id="0f387-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="0f387-116">如果我們需要在兩個不同的演算法中安全地使用單一 entropic 值，我們就不需要知道演算法的實際用途。</span><span class="sxs-lookup"><span data-stu-id="0f387-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="0f387-117">我們真正關心的是它們的行為。</span><span class="sxs-lookup"><span data-stu-id="0f387-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="0f387-118">任何適當的對稱區塊加密演算法也都是強式隨機的 (PRP) ：修正輸入 (索引鍵、連結模式、IV、純文字) 和加密文字輸出的機率，與其他任何對稱式區塊加密演算法（指定相同的輸入）不同。</span><span class="sxs-lookup"><span data-stu-id="0f387-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="0f387-119">同樣地，任何適當的索引雜湊函式也是強式虛擬亂數函式 (PRF) ，而且在指定固定的輸入集時，其輸出會回應非常正面與任何其他的索引雜湊函數不同。</span><span class="sxs-lookup"><span data-stu-id="0f387-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="0f387-120">我們會使用此強式 Prp 和 PRFs 的概念來建立內容標頭。</span><span class="sxs-lookup"><span data-stu-id="0f387-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="0f387-121">此內容標頭基本上可作為任何指定作業所使用的演算法的穩定指紋，並提供資料保護系統所需的密碼編譯靈活性。</span><span class="sxs-lookup"><span data-stu-id="0f387-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="0f387-122">此標頭是可重現的，稍後用來作為子機碼 [衍生](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)程式的一部分。</span><span class="sxs-lookup"><span data-stu-id="0f387-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="0f387-123">有兩種不同的方式可根據基礎演算法的操作模式來建立內容標頭。</span><span class="sxs-lookup"><span data-stu-id="0f387-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="0f387-124">CBC 模式加密 + HMAC 驗證</span><span class="sxs-lookup"><span data-stu-id="0f387-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="0f387-125">內容標頭包含下列元件：</span><span class="sxs-lookup"><span data-stu-id="0f387-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="0f387-126">[16 位]值 00 00，這是標示為「CBC 加密 + HMAC 驗證」的標記。</span><span class="sxs-lookup"><span data-stu-id="0f387-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="0f387-127">[32 位]對稱區塊加密演算法的金鑰長度 (位元組、位元組由大到小的) 。</span><span class="sxs-lookup"><span data-stu-id="0f387-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="0f387-128">[32 位]區塊大小 (對稱區塊加密演算法的位元組位元組由大到小的) 。</span><span class="sxs-lookup"><span data-stu-id="0f387-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="0f387-129">[32 位]HMAC 演算法的金鑰長度 (位元組、位元組由大到小的) 。</span><span class="sxs-lookup"><span data-stu-id="0f387-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="0f387-130"> (目前的金鑰大小一律符合摘要大小。 ) </span><span class="sxs-lookup"><span data-stu-id="0f387-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="0f387-131">[32 位]以位元組為單位的摘要大小 (HMAC 演算法的位元組由) 大到小。</span><span class="sxs-lookup"><span data-stu-id="0f387-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="0f387-132">`EncCBC(K_E, IV, "")`，此為對稱區塊加密演算法的輸出，指定空字串輸入，其中 IV 是全零的向量。</span><span class="sxs-lookup"><span data-stu-id="0f387-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="0f387-133">的結構 `K_E` 如下所述。</span><span class="sxs-lookup"><span data-stu-id="0f387-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="0f387-134">`MAC(K_H, "")`，這是指定空字串輸入的 HMAC 演算法輸出。</span><span class="sxs-lookup"><span data-stu-id="0f387-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="0f387-135">的結構 `K_H` 如下所述。</span><span class="sxs-lookup"><span data-stu-id="0f387-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="0f387-136">在理想的情況下，我們可以傳遞和的全零向量 `K_E` `K_H` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="0f387-137">不過，在執行任何)  (作業之前，我們想要避免基礎演算法檢查是否存在弱式金鑰，而不是使用簡單或可重複的模式，例如全零的向量。</span><span class="sxs-lookup"><span data-stu-id="0f387-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="0f387-138">相反地，我們會在計數器模式中使用 NIST SP800-108 KDF (查看 [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，Sec. 5.1) 具有長度為零的索引鍵、標籤和內容，以及 HMACSHA512 作為基礎 PRF。</span><span class="sxs-lookup"><span data-stu-id="0f387-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="0f387-139">我們會衍生 `| K_E | + | K_H |` 位元組的輸出，然後將結果分解為 `K_E` 和 `K_H` 本身。</span><span class="sxs-lookup"><span data-stu-id="0f387-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="0f387-140">這會以數學方式表示，如下所示。</span><span class="sxs-lookup"><span data-stu-id="0f387-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="0f387-141">範例： AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="0f387-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="0f387-142">例如，假設對稱式區塊加密演算法是 AES-192-CBC 且驗證演算法為 HMACSHA256 的情況。</span><span class="sxs-lookup"><span data-stu-id="0f387-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="0f387-143">系統會使用下列步驟來產生內容標頭。</span><span class="sxs-lookup"><span data-stu-id="0f387-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="0f387-144">First、let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 、where `| K_E | = 192 bits` 和 `| K_H | = 256 bits` per 指定的演算法。</span><span class="sxs-lookup"><span data-stu-id="0f387-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="0f387-145">這會導致 `K_E = 5BB6..21DD` `K_H = A04A..00A9` 下列範例中的和：</span><span class="sxs-lookup"><span data-stu-id="0f387-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="0f387-146">接下來，計算 `Enc_CBC (K_E, IV, "")` 指定和以上的 AES-192-CBC `IV = 0*` `K_E` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="0f387-147">接下來，請計算 `MAC(K_H, "")` 上述指定的 HMACSHA256 `K_H` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="0f387-148">這會產生以下的完整內容標頭：</span><span class="sxs-lookup"><span data-stu-id="0f387-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="0f387-149">此內容標頭是驗證加密演算法組的指紋， (AES-192-CBC 加密 + HMACSHA256 驗證) 。</span><span class="sxs-lookup"><span data-stu-id="0f387-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="0f387-150">[上述](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)元件如下所述：</span><span class="sxs-lookup"><span data-stu-id="0f387-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="0f387-151">標記 `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="0f387-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="0f387-152">區塊加密金鑰長度 `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="0f387-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="0f387-153">區塊加密區塊大小 `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="0f387-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="0f387-154">HMAC 金鑰長度 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="0f387-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="0f387-155">HMAC 摘要大小 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="0f387-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="0f387-156">區塊加密 PRP 輸出 `(F4 74 - DB 6F)` 和</span><span class="sxs-lookup"><span data-stu-id="0f387-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="0f387-157">HMAC PRF 輸出 `(D4 79 - end)` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="0f387-158">CBC 模式加密 + HMAC 驗證內容標頭的建立方式相同，不論演算法的執行是由 Windows CNG 或 managed SymmetricAlgorithm 和 KeyedHashAlgorithm 類型提供。</span><span class="sxs-lookup"><span data-stu-id="0f387-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="0f387-159">如此一來，在不同作業系統上執行的應用程式就能可靠地產生相同的內容標頭，即使在作業系統之間的演算法是不同的。</span><span class="sxs-lookup"><span data-stu-id="0f387-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="0f387-160"> (實務上，KeyedHashAlgorithm 不必是正確的 HMAC。</span><span class="sxs-lookup"><span data-stu-id="0f387-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="0f387-161">它可以是任何金鑰雜湊演算法類型。 ) </span><span class="sxs-lookup"><span data-stu-id="0f387-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="0f387-162">範例： 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="0f387-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="0f387-163">First、let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 、where `| K_E | = 192 bits` 和 `| K_H | = 160 bits` per 指定的演算法。</span><span class="sxs-lookup"><span data-stu-id="0f387-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="0f387-164">這會導致 `K_E = A219..E2BB` `K_H = DC4A..B464` 下列範例中的和：</span><span class="sxs-lookup"><span data-stu-id="0f387-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="0f387-165">接下來，計算 `Enc_CBC (K_E, IV, "")` 所提供的 3des-192-CBC， `IV = 0*` `K_E` 如上所示。</span><span class="sxs-lookup"><span data-stu-id="0f387-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="0f387-166">接下來，請計算 `MAC(K_H, "")` 上述指定的 HMACSHA1 `K_H` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="0f387-167">這會產生完整的內容標頭，其為已驗證加密演算法組的指紋 (3DES-192-CBC encryption + HMACSHA1 驗證) ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0f387-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="0f387-168">元件的細分方式如下：</span><span class="sxs-lookup"><span data-stu-id="0f387-168">The components break down as follows:</span></span>

* <span data-ttu-id="0f387-169">標記 `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="0f387-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="0f387-170">區塊加密金鑰長度 `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="0f387-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="0f387-171">區塊加密區塊大小 `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="0f387-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="0f387-172">HMAC 金鑰長度 `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="0f387-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="0f387-173">HMAC 摘要大小 `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="0f387-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="0f387-174">區塊加密 PRP 輸出 `(AB B1 - E1 0E)` 和</span><span class="sxs-lookup"><span data-stu-id="0f387-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="0f387-175">HMAC PRF 輸出 `(76 EB - end)` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="0f387-176">Galois/計數器模式加密 + 驗證</span><span class="sxs-lookup"><span data-stu-id="0f387-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="0f387-177">內容標頭包含下列元件：</span><span class="sxs-lookup"><span data-stu-id="0f387-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="0f387-178">[16 位]值 00 01，這是表示「GCM 加密 + 驗證」的標記。</span><span class="sxs-lookup"><span data-stu-id="0f387-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="0f387-179">[32 位]對稱區塊加密演算法的金鑰長度 (位元組、位元組由大到小的) 。</span><span class="sxs-lookup"><span data-stu-id="0f387-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="0f387-180">[32 位]在經過驗證的加密作業期間，nonce 大小 (以位元組為單位、以位元組為單位的) 使用。</span><span class="sxs-lookup"><span data-stu-id="0f387-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="0f387-181">針對我們的系統 (，這會在 nonce 大小 = 96 位修正。 ) </span><span class="sxs-lookup"><span data-stu-id="0f387-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="0f387-182">[32 位]區塊大小 (對稱區塊加密演算法的位元組位元組由大到小的) 。</span><span class="sxs-lookup"><span data-stu-id="0f387-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="0f387-183">針對 GCM (，這會在區塊大小 = 128 個位修正。 ) </span><span class="sxs-lookup"><span data-stu-id="0f387-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="0f387-184">[32 位]驗證標記大小 (以位元組為單位，由已驗證加密函式產生的位元組由) 大到小。</span><span class="sxs-lookup"><span data-stu-id="0f387-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="0f387-185">針對我們的系統 (，這會在標記大小 = 128 個位修正。 ) </span><span class="sxs-lookup"><span data-stu-id="0f387-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="0f387-186">[128 位]的標記 `Enc_GCM (K_E, nonce, "")` ，也就是對稱區塊加密演算法的輸出，指定空字串輸入，其中 nonce 是96位全零的向量。</span><span class="sxs-lookup"><span data-stu-id="0f387-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="0f387-187">`K_E` 是使用與 CBC 加密 + HMAC 驗證案例中的相同機制所衍生。</span><span class="sxs-lookup"><span data-stu-id="0f387-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="0f387-188">不過，由於這裡沒有任何 `K_H` 作用，我們基本上會有 `| K_H | = 0` ，而且演算法會折迭到以下形式。</span><span class="sxs-lookup"><span data-stu-id="0f387-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="0f387-189">範例： AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="0f387-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="0f387-190">First、let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 、where `| K_E | = 256 bits` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="0f387-191">接下來，計算 `Enc_GCM (K_E, nonce, "")` 指定和以上的 AES-256-GCM 的驗證標記 `nonce = 096` `K_E` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="0f387-192">這會產生以下的完整內容標頭：</span><span class="sxs-lookup"><span data-stu-id="0f387-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="0f387-193">元件的細分方式如下：</span><span class="sxs-lookup"><span data-stu-id="0f387-193">The components break down as follows:</span></span>

* <span data-ttu-id="0f387-194">標記 `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="0f387-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="0f387-195">區塊加密金鑰長度 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="0f387-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="0f387-196">nonce 大小 `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="0f387-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="0f387-197">區塊加密區塊大小 `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="0f387-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="0f387-198">驗證標記大小 `(00 00 00 10)` 和</span><span class="sxs-lookup"><span data-stu-id="0f387-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="0f387-199">執行區塊密碼的驗證標記 `(E7 DC - end)` 。</span><span class="sxs-lookup"><span data-stu-id="0f387-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
