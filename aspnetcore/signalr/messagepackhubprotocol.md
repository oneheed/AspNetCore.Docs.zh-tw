---
title: 使用中的 MessagePack Hub 通訊協定 SignalR 進行 ASP.NET Core
author: bradygaster
description: 將 MessagePack 中樞通訊協定新增至 ASP.NET Core SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024687"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="eb61a-103">使用中的 MessagePack Hub 通訊協定 SignalR 進行 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb61a-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="eb61a-104">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eb61a-105">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="eb61a-105">What is MessagePack?</span></span>

<span data-ttu-id="eb61a-106">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eb61a-107">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="eb61a-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eb61a-108">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="eb61a-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eb61a-109">SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="eb61a-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eb61a-110">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb61a-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="eb61a-111">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="eb61a-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eb61a-112">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-113">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="eb61a-113">JSON is enabled by default.</span></span> <span data-ttu-id="eb61a-114">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="eb61a-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eb61a-115">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eb61a-116">在該委派中， `SerializerOptions` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eb61a-117">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eb61a-118">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="eb61a-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="eb61a-119">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eb61a-120">例如， `.WithSecurity(MessagePackSecurity.UntrustedData)` 在取代時呼叫 `SerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eb61a-121">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb61a-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-122">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="eb61a-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eb61a-123">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="eb61a-124">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eb61a-125">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-125">.NET client</span></span>

<span data-ttu-id="eb61a-126">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eb61a-127">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="eb61a-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eb61a-128">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-128">JavaScript client</span></span>

<span data-ttu-id="eb61a-129">JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="eb61a-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eb61a-130">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="eb61a-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="eb61a-131">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="eb61a-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eb61a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eb61a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="eb61a-133">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eb61a-134">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="eb61a-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eb61a-135">您可以在 *node_modules\msgpack5\dist\msgpack5.js* 找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-136">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="eb61a-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eb61a-137">如果在 *msgpack5.js* 之前參考 *signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="eb61a-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eb61a-138">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eb61a-139">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb61a-140">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="eb61a-141">Java 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-141">Java client</span></span>

<span data-ttu-id="eb61a-142">若要使用 JAVA 啟用 MessagePack，請安裝 `com.microsoft.signalr.messagepack` 套件。</span><span class="sxs-lookup"><span data-stu-id="eb61a-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="eb61a-143">使用 Gradle 時，請將下列這一行新增至 `dependencies` *Gradle* 檔案的區段：</span><span class="sxs-lookup"><span data-stu-id="eb61a-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="eb61a-144">使用 Maven 時，請將下列幾行新增至 `<dependencies>` *pom.xml* 檔案的元素內：</span><span class="sxs-lookup"><span data-stu-id="eb61a-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="eb61a-145">`withHubProtocol(new MessagePackHubProtocol())`在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="eb61a-146">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="eb61a-146">MessagePack quirks</span></span>

<span data-ttu-id="eb61a-147">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eb61a-148">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="eb61a-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eb61a-149">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eb61a-150">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="eb61a-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eb61a-151">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="eb61a-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eb61a-152">例如：</span><span class="sxs-lookup"><span data-stu-id="eb61a-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eb61a-153">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="eb61a-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eb61a-154">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eb61a-155">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eb61a-156">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="eb61a-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eb61a-157">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eb61a-158">如此一來，當您將日期還原序列化時，如果是，MessagePack Hub 通訊協定將會轉換成 UTC 格式， `DateTime.Kind` `DateTimeKind.Local` 否則它將不會觸及時間並依原樣傳遞。</span><span class="sxs-lookup"><span data-stu-id="eb61a-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="eb61a-159">如果您正在使用 `DateTime` 值，建議您在傳送之前轉換為 UTC。</span><span class="sxs-lookup"><span data-stu-id="eb61a-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eb61a-160">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="eb61a-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eb61a-161">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="eb61a-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eb61a-162">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="eb61a-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eb61a-163">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="eb61a-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eb61a-164">的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eb61a-165">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="eb61a-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eb61a-166">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="eb61a-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eb61a-167">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="eb61a-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eb61a-168">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="eb61a-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eb61a-169">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eb61a-170">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="eb61a-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eb61a-171">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="eb61a-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eb61a-172">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="eb61a-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eb61a-173">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="eb61a-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eb61a-174">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="eb61a-175">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="eb61a-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eb61a-176">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="eb61a-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eb61a-177">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="eb61a-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eb61a-178">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="eb61a-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eb61a-179">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="eb61a-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eb61a-180">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="eb61a-181">JAVA 中的字元和字串</span><span class="sxs-lookup"><span data-stu-id="eb61a-181">Chars and Strings in Java</span></span>

<span data-ttu-id="eb61a-182">在 java 用戶端中， `char` 物件會序列化為一個字元的 `String` 物件。</span><span class="sxs-lookup"><span data-stu-id="eb61a-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="eb61a-183">這與 c # 和 JavaScript 用戶端相反，它會將它們序列化為 `short` 物件。</span><span class="sxs-lookup"><span data-stu-id="eb61a-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="eb61a-184">MessagePack 規格本身不會定義物件的行為 `char` ，因此可由程式庫作者決定如何將其序列化。</span><span class="sxs-lookup"><span data-stu-id="eb61a-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="eb61a-185">用戶端之間的行為差異是我們用於執行的程式庫結果。</span><span class="sxs-lookup"><span data-stu-id="eb61a-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="eb61a-186">相關資源</span><span class="sxs-lookup"><span data-stu-id="eb61a-186">Related resources</span></span>

* [<span data-ttu-id="eb61a-187">開始使用</span><span class="sxs-lookup"><span data-stu-id="eb61a-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eb61a-188">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eb61a-189">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="eb61a-190">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eb61a-191">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="eb61a-191">What is MessagePack?</span></span>

<span data-ttu-id="eb61a-192">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eb61a-193">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="eb61a-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eb61a-194">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="eb61a-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eb61a-195">SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="eb61a-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eb61a-196">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb61a-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="eb61a-197">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="eb61a-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eb61a-198">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-199">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="eb61a-199">JSON is enabled by default.</span></span> <span data-ttu-id="eb61a-200">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="eb61a-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eb61a-201">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eb61a-202">在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eb61a-203">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eb61a-204">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="eb61a-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="eb61a-205">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eb61a-206">例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="eb61a-207">設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="eb61a-208">安裝 `MessagePack` 1.9. x 版升級時 SignalR 使用。</span><span class="sxs-lookup"><span data-stu-id="eb61a-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="eb61a-209">`MessagePack` 2.x 版引進了重大變更，與 SignalR 3.1 版及更早版本不相容。</span><span class="sxs-lookup"><span data-stu-id="eb61a-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="eb61a-210">當 `MessagePackSecurity.Active` 未設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成拒絕服務。</span><span class="sxs-lookup"><span data-stu-id="eb61a-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="eb61a-211">`MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="eb61a-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eb61a-212">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb61a-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-213">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="eb61a-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eb61a-214">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="eb61a-215">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eb61a-216">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-216">.NET client</span></span>

<span data-ttu-id="eb61a-217">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eb61a-218">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="eb61a-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eb61a-219">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-219">JavaScript client</span></span>

<span data-ttu-id="eb61a-220">JavaScript 用戶端的 MessagePack 支援是由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="eb61a-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eb61a-221">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="eb61a-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="eb61a-222">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="eb61a-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eb61a-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eb61a-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="eb61a-224">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eb61a-225">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="eb61a-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eb61a-226">您可以在 *node_modules\msgpack5\dist\msgpack5.js* 找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-227">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="eb61a-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eb61a-228">如果在 *msgpack5.js* 之前參考 *signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="eb61a-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eb61a-229">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eb61a-230">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb61a-231">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eb61a-232">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="eb61a-232">MessagePack quirks</span></span>

<span data-ttu-id="eb61a-233">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eb61a-234">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="eb61a-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eb61a-235">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eb61a-236">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="eb61a-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eb61a-237">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="eb61a-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eb61a-238">例如：</span><span class="sxs-lookup"><span data-stu-id="eb61a-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eb61a-239">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="eb61a-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eb61a-240">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eb61a-241">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eb61a-242">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="eb61a-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eb61a-243">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eb61a-244">如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="eb61a-245">如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。</span><span class="sxs-lookup"><span data-stu-id="eb61a-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eb61a-246">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="eb61a-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="eb61a-247">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eb61a-248">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="eb61a-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eb61a-249">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="eb61a-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eb61a-250">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="eb61a-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eb61a-251">的值 `DateTime.MinValue` 為 `January 1, 0001` ，必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eb61a-252">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="eb61a-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eb61a-253">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="eb61a-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eb61a-254">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="eb61a-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eb61a-255">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="eb61a-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eb61a-256">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eb61a-257">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="eb61a-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eb61a-258">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="eb61a-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eb61a-259">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="eb61a-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eb61a-260">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="eb61a-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eb61a-261">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="eb61a-262">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="eb61a-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eb61a-263">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="eb61a-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eb61a-264">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="eb61a-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eb61a-265">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="eb61a-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eb61a-266">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="eb61a-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eb61a-267">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eb61a-268">相關資源</span><span class="sxs-lookup"><span data-stu-id="eb61a-268">Related resources</span></span>

* [<span data-ttu-id="eb61a-269">開始使用</span><span class="sxs-lookup"><span data-stu-id="eb61a-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eb61a-270">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eb61a-271">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb61a-272">本文假設讀者已熟悉 [開始](xref:tutorials/signalr)所涵蓋的主題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eb61a-273">什麼是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="eb61a-273">What is MessagePack?</span></span>

<span data-ttu-id="eb61a-274">[MessagePack](https://msgpack.org/index.html) 是一種快速且精簡的二進位序列化格式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eb61a-275">當效能和頻寬有問題時，這會很有用，因為它會建立比 [JSON](https://www.json.org/)更小的訊息。</span><span class="sxs-lookup"><span data-stu-id="eb61a-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eb61a-276">在查看網路追蹤和記錄檔時，除非透過 MessagePack 剖析器傳遞位元組，否則二進位訊息無法讀取。</span><span class="sxs-lookup"><span data-stu-id="eb61a-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eb61a-277">SignalR 內建支援 MessagePack 格式，並提供 Api 供用戶端和伺服器使用。</span><span class="sxs-lookup"><span data-stu-id="eb61a-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eb61a-278">在伺服器上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb61a-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="eb61a-279">若要在伺服器上啟用 MessagePack Hub 通訊協定，請 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在您的應用程式中安裝套件。</span><span class="sxs-lookup"><span data-stu-id="eb61a-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eb61a-280">在 `Startup.ConfigureServices` 方法中，新增 `AddMessagePackProtocol` 至在 `AddSignalR` 伺服器上啟用 MessagePack 支援的呼叫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-281">預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="eb61a-281">JSON is enabled by default.</span></span> <span data-ttu-id="eb61a-282">新增 MessagePack 可支援 JSON 和 MessagePack 用戶端。</span><span class="sxs-lookup"><span data-stu-id="eb61a-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eb61a-283">若要自訂 MessagePack 將如何格式化您的資料，請 `AddMessagePackProtocol` 使用委派來設定選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eb61a-284">在該委派中， `FormatterResolvers` 屬性可以用來設定 MessagePack 序列化選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eb61a-285">如需解析程式如何運作的詳細資訊，請造訪 MessagePack library，網址為 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eb61a-286">屬性可用於您要序列化的物件，以定義應該如何處理它們。</span><span class="sxs-lookup"><span data-stu-id="eb61a-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="eb61a-287">強烈建議您檢查 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 並套用建議的修補程式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eb61a-288">例如，將 `MessagePackSecurity.Active` 靜態屬性設定為 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="eb61a-289">設定 `MessagePackSecurity.Active` 需要手動安裝 MessagePack 的 [1.9. x 版](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="eb61a-290">安裝 `MessagePack` 1.9. x 版升級時 SignalR 使用。</span><span class="sxs-lookup"><span data-stu-id="eb61a-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="eb61a-291">當未 `MessagePackSecurity.Active` 設定為時 `MessagePackSecurity.UntrustedData` ，惡意用戶端可能會造成阻絕服務。</span><span class="sxs-lookup"><span data-stu-id="eb61a-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="eb61a-292">`MessagePackSecurity.Active`在中設定 `Program.Main` ，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="eb61a-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eb61a-293">在用戶端上設定 MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb61a-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-294">針對支援的用戶端，預設會啟用 JSON。</span><span class="sxs-lookup"><span data-stu-id="eb61a-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eb61a-295">用戶端只能支援單一通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="eb61a-296">新增 MessagePack 支援將會取代任何先前設定的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eb61a-297">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-297">.NET client</span></span>

<span data-ttu-id="eb61a-298">若要在 .NET 用戶端中啟用 MessagePack，請安裝 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 封裝並 `AddMessagePackProtocol` 在上呼叫 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eb61a-299">此 `AddMessagePackProtocol` 呼叫會採用委派來設定選項，就像伺服器一樣。</span><span class="sxs-lookup"><span data-stu-id="eb61a-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eb61a-300">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-300">JavaScript client</span></span>

<span data-ttu-id="eb61a-301">JavaScript 用戶端的 MessagePack 支援是由 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm 封裝提供。</span><span class="sxs-lookup"><span data-stu-id="eb61a-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eb61a-302">在命令 shell 中執行下列命令，以安裝套件：</span><span class="sxs-lookup"><span data-stu-id="eb61a-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="eb61a-303">安裝 npm 套件之後，您可以透過 JavaScript 模組載入器直接使用模組，或藉由參考下列檔案將其匯入至瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="eb61a-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eb61a-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eb61a-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="eb61a-305">在瀏覽器中， `msgpack5` 也必須參考程式庫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eb61a-306">使用 `<script>` 標記來建立參考。</span><span class="sxs-lookup"><span data-stu-id="eb61a-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eb61a-307">您可以在 *node_modules\msgpack5\dist\msgpack5.js* 找到該程式庫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="eb61a-308">使用專案時 `<script>` ，順序很重要。</span><span class="sxs-lookup"><span data-stu-id="eb61a-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eb61a-309">如果在 *msgpack5.js* 之前參考 *signalr-protocol-msgpack.js* ，則嘗試使用 MessagePack 連接時，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="eb61a-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eb61a-310">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eb61a-311">新增 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 至 `HubConnectionBuilder` 會將用戶端設定為在連接到伺服器時使用 MessagePack 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="eb61a-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb61a-312">目前，JavaScript 用戶端上沒有 MessagePack 通訊協定的設定選項。</span><span class="sxs-lookup"><span data-stu-id="eb61a-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eb61a-313">MessagePack 的不相容</span><span class="sxs-lookup"><span data-stu-id="eb61a-313">MessagePack quirks</span></span>

<span data-ttu-id="eb61a-314">使用 MessagePack 中樞通訊協定時，有幾個要注意的問題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eb61a-315">MessagePack 區分大小寫</span><span class="sxs-lookup"><span data-stu-id="eb61a-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eb61a-316">MessagePack 通訊協定會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="eb61a-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eb61a-317">例如，請考慮下列 c # 類別：</span><span class="sxs-lookup"><span data-stu-id="eb61a-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eb61a-318">從 JavaScript 用戶端傳送時，您必須使用 `PascalCased` 屬性名稱，因為大小寫必須完全符合 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="eb61a-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eb61a-319">例如：</span><span class="sxs-lookup"><span data-stu-id="eb61a-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eb61a-320">使用 `camelCased` 名稱並不會正確地系結至 c # 類別。</span><span class="sxs-lookup"><span data-stu-id="eb61a-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eb61a-321">您可以使用 `Key` 屬性來為 MessagePack 屬性指定不同的名稱，藉此解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="eb61a-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eb61a-322">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eb61a-323">序列化/還原序列化時，不會保留 DateTime. 種類</span><span class="sxs-lookup"><span data-stu-id="eb61a-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eb61a-324">MessagePack 通訊協定不提供將的值編碼的方式 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eb61a-325">如此一來，在還原序列化日期時，MessagePack Hub 通訊協定會假設內送日期採用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="eb61a-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="eb61a-326">如果您是 `DateTime` 以當地時間處理值，建議您在傳送之前轉換成 UTC。</span><span class="sxs-lookup"><span data-stu-id="eb61a-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eb61a-327">當您收到時，請將它們從 UTC 轉換為當地時間。</span><span class="sxs-lookup"><span data-stu-id="eb61a-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="eb61a-328">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eb61a-329">JavaScript 中的 MessagePack 不支援 MinValue</span><span class="sxs-lookup"><span data-stu-id="eb61a-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eb61a-330">JavaScript 用戶端所使用的 [msgpack5](https://github.com/mcollina/msgpack5) 程式庫 SignalR 不支援 `timestamp96` MessagePack 中的類型。</span><span class="sxs-lookup"><span data-stu-id="eb61a-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eb61a-331">這種類型是用來編碼非常大的日期值， (在過去或未來的) 中非常早。</span><span class="sxs-lookup"><span data-stu-id="eb61a-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eb61a-332">的值 `DateTime.MinValue` `January 1, 0001` 必須以值進行編碼 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="eb61a-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eb61a-333">基於這個原因， `DateTime.MinValue` 不支援傳送至 JavaScript 用戶端。</span><span class="sxs-lookup"><span data-stu-id="eb61a-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eb61a-334">當 `DateTime.MinValue` JavaScript 用戶端收到時，會擲回下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="eb61a-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eb61a-335">通常 `DateTime.MinValue` 是用來編碼「遺失」或 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="eb61a-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eb61a-336">如果您需要在 MessagePack 中編碼該值，請使用可為 null 的 `DateTime` 值 (`DateTime?`) 或編碼個別 `bool` 值，以指出日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="eb61a-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eb61a-337">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eb61a-338">在「事先」編譯環境中 MessagePack 支援</span><span class="sxs-lookup"><span data-stu-id="eb61a-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eb61a-339">.NET 用戶端和伺服器所使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 程式庫會使用程式碼產生，將序列化優化。</span><span class="sxs-lookup"><span data-stu-id="eb61a-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eb61a-340">因此，在使用「預先」編譯 (（例如 Xamarin iOS 或 Unity) ）的環境上，預設不會支援此方法。</span><span class="sxs-lookup"><span data-stu-id="eb61a-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eb61a-341">您可以藉由「預先產生」序列化程式/還原序列化程式碼，在這些環境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="eb61a-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eb61a-342">如需詳細資訊，請參閱 [MessagePack-CSharp 檔](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="eb61a-343">當您預先產生序列化程式之後，您可以使用傳遞至的設定委派來註冊它們 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="eb61a-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eb61a-344">MessagePack 中的類型檢查較嚴格</span><span class="sxs-lookup"><span data-stu-id="eb61a-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eb61a-345">JSON Hub 通訊協定會在還原序列化期間執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="eb61a-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eb61a-346">例如，如果傳入物件的屬性值是數位 (`{ foo: 42 }`) 但 .net 類別上的屬性是類型 `string` ，則值將會轉換。</span><span class="sxs-lookup"><span data-stu-id="eb61a-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eb61a-347">但是，MessagePack 不會執行這項轉換，而且會擲回例外狀況，在伺服器端記錄檔 (和主控台) 中會出現此例外狀況：</span><span class="sxs-lookup"><span data-stu-id="eb61a-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eb61a-348">如需這項限制的詳細資訊，請參閱 GitHub 問題 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="eb61a-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eb61a-349">相關資源</span><span class="sxs-lookup"><span data-stu-id="eb61a-349">Related resources</span></span>

* [<span data-ttu-id="eb61a-350">開始使用</span><span class="sxs-lookup"><span data-stu-id="eb61a-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eb61a-351">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eb61a-352">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="eb61a-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
