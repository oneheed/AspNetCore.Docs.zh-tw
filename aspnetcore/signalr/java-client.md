---
title: ASP.NET Core SignalR JAVA 用戶端
author: mikaelm12
description: 瞭解如何使用 ASP.NET Core SignalR JAVA 用戶端。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: 92941d21820de90eb2ae8fb76c21c588ed9f1ffb
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024752"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="40a10-103">ASP.NET Core SignalR JAVA 用戶端</span><span class="sxs-lookup"><span data-stu-id="40a10-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="40a10-104">依 [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="40a10-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="40a10-105">JAVA 用戶端可 SignalR 從 java 程式碼（包括 Android 應用程式）連接到 ASP.NET Core 的伺服器。</span><span class="sxs-lookup"><span data-stu-id="40a10-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="40a10-106">JAVA 用戶端和 [JavaScript 客戶](xref:signalr/javascript-client) 端和 [.net 客戶](xref:signalr/dotnet-client)端一樣，可讓您即時接收和傳送訊息至中樞。</span><span class="sxs-lookup"><span data-stu-id="40a10-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="40a10-107">JAVA 用戶端可在 ASP.NET Core 2.2 和更新版本中使用。</span><span class="sxs-lookup"><span data-stu-id="40a10-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="40a10-108">本文中參考的 JAVA 主控台應用程式範例會使用 SignalR java 用戶端。</span><span class="sxs-lookup"><span data-stu-id="40a10-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="40a10-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="40a10-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="40a10-110">安裝 SignalR JAVA 用戶端套件</span><span class="sxs-lookup"><span data-stu-id="40a10-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="40a10-111">*Signalr 1.0.0* JAR 檔案可讓用戶端連接至 SignalR 中樞。</span><span class="sxs-lookup"><span data-stu-id="40a10-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="40a10-112">若要尋找最新的 JAR 檔案版本號碼，請參閱 [Maven 搜尋結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)。</span><span class="sxs-lookup"><span data-stu-id="40a10-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="40a10-113">如果使用 Gradle，請將下列這一行新增至 `dependencies` *Gradle* 檔案的區段：</span><span class="sxs-lookup"><span data-stu-id="40a10-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="40a10-114">如果使用 Maven，請將下列幾行新增至 `<dependencies>` *pom.xml* 檔案的元素內：</span><span class="sxs-lookup"><span data-stu-id="40a10-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="40a10-115">連接至中樞</span><span class="sxs-lookup"><span data-stu-id="40a10-115">Connect to a hub</span></span>

<span data-ttu-id="40a10-116">若要建立 `HubConnection` ，則 `HubConnectionBuilder` 應該使用。</span><span class="sxs-lookup"><span data-stu-id="40a10-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="40a10-117">建立連接時，可以設定中樞 URL 和記錄層級。</span><span class="sxs-lookup"><span data-stu-id="40a10-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="40a10-118">在之前呼叫任何方法，以設定任何必要的選項 `HubConnectionBuilder` `build` 。</span><span class="sxs-lookup"><span data-stu-id="40a10-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="40a10-119">開始與的連接 `start` 。</span><span class="sxs-lookup"><span data-stu-id="40a10-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="40a10-120">從用戶端呼叫中樞方法</span><span class="sxs-lookup"><span data-stu-id="40a10-120">Call hub methods from client</span></span>

<span data-ttu-id="40a10-121">呼叫來叫 `send` 用中樞方法。</span><span class="sxs-lookup"><span data-stu-id="40a10-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="40a10-122">將中樞方法中定義的中樞方法名稱和任何引數傳遞至 `send` 。</span><span class="sxs-lookup"><span data-stu-id="40a10-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="40a10-123">只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。</span><span class="sxs-lookup"><span data-stu-id="40a10-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="40a10-124">如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。</span><span class="sxs-lookup"><span data-stu-id="40a10-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="40a10-125">從中樞呼叫用戶端方法</span><span class="sxs-lookup"><span data-stu-id="40a10-125">Call client methods from hub</span></span>

<span data-ttu-id="40a10-126">用 `hubConnection.on` 來定義用戶端上中樞可以呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="40a10-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="40a10-127">在建立之後，但在開始連接之前定義方法。</span><span class="sxs-lookup"><span data-stu-id="40a10-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="40a10-128">新增記錄</span><span class="sxs-lookup"><span data-stu-id="40a10-128">Add logging</span></span>

<span data-ttu-id="40a10-129">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="40a10-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="40a10-130">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="40a10-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="40a10-131">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="40a10-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="40a10-132">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="40a10-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="40a10-133">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="40a10-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="40a10-134">Android 開發附注</span><span class="sxs-lookup"><span data-stu-id="40a10-134">Android development notes</span></span>

<span data-ttu-id="40a10-135">在 Android SDK 用戶端功能的相容性方面 SignalR ，指定目標 Android SDK 版本時，請考慮下列專案：</span><span class="sxs-lookup"><span data-stu-id="40a10-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="40a10-136">SignalRJAVA 用戶端會在 ANDROID API 層級16和更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="40a10-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="40a10-137">透過 Azure 服務連接 SignalR 將需要 ANDROID API 層級20和更新版本，因為 [azure SignalR 服務](/azure/azure-signalr/signalr-overview) 需要 TLS 1.2 且不支援 sha-1 型加密套件。</span><span class="sxs-lookup"><span data-stu-id="40a10-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="40a10-138">Android 在 API 層級20中 [新增了 SHA-256 (和以上) 加密套件的支援](https://developer.android.com/reference/javax/net/ssl/SSLSocket) 。</span><span class="sxs-lookup"><span data-stu-id="40a10-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="40a10-139">設定持有人權杖驗證</span><span class="sxs-lookup"><span data-stu-id="40a10-139">Configure bearer token authentication</span></span>

<span data-ttu-id="40a10-140">在 SignalR JAVA 用戶端中，您可以將「存取權杖處理站」提供給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="40a10-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="40a10-141">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="40a10-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="40a10-142">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="40a10-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a><span data-ttu-id="40a10-143">在 JAVA 中傳遞類別資訊</span><span class="sxs-lookup"><span data-stu-id="40a10-143">Passing Class information in Java</span></span>

<span data-ttu-id="40a10-144">`on` `invoke` `stream` `HubConnection` 在 JAVA 用戶端中呼叫、或方法時，使用者應該傳遞 `Type` 物件而不是 `Class<?>` 物件，以描述任何傳遞給方法的泛型 `Object` 。</span><span class="sxs-lookup"><span data-stu-id="40a10-144">When calling the `on`, `invoke`, or `stream` methods of `HubConnection` in the Java client, users should pass a `Type` object rather than a `Class<?>` object to describe any generic `Object` passed to the method.</span></span> <span data-ttu-id="40a10-145">`Type`可以使用提供的 `TypeReference` 類別取得。</span><span class="sxs-lookup"><span data-stu-id="40a10-145">A `Type` can be acquired using the provided `TypeReference` class.</span></span> <span data-ttu-id="40a10-146">例如，使用名為的自訂泛型類別 `Foo<T>` ，下列程式碼會取得 `Type` ：</span><span class="sxs-lookup"><span data-stu-id="40a10-146">For example, using a custom generic class named `Foo<T>`, the following code gets the `Type`:</span></span>

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

<span data-ttu-id="40a10-147">針對非泛型（例如基本類型或其他非參數化型別 `String` ），您可以直接使用內建 `.class` 。</span><span class="sxs-lookup"><span data-stu-id="40a10-147">For non-generics, such as primitives or other non-parameterized types like `String`, you can simply use the built-in `.class`.</span></span>

<span data-ttu-id="40a10-148">使用一或多個物件類型呼叫其中一個方法時，請在叫用方法時使用泛型語法。</span><span class="sxs-lookup"><span data-stu-id="40a10-148">When calling one of these methods with one or more object types, use the generics syntax when invoking the method.</span></span> <span data-ttu-id="40a10-149">例如，註冊 `on` 名為之方法的處理常式時 `func` ，會以字串和物件做為引數 `Foo<String>` ，請使用下列程式碼來設定列印引數的動作：</span><span class="sxs-lookup"><span data-stu-id="40a10-149">For example, when registering an `on` handler for a method named `func`, which takes as arguments a String and a `Foo<String>` object, use the following code to set an action to print the arguments:</span></span>

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

<span data-ttu-id="40a10-150">這是必要的慣例，因為我們無法使用方法來取得複雜類型的完整資訊，因為 `Object.getClass` JAVA 中的型別抹除。</span><span class="sxs-lookup"><span data-stu-id="40a10-150">This convention is necessary because we can not retrieve complete information about complex types with the `Object.getClass` method due to type erasure in Java.</span></span> <span data-ttu-id="40a10-151">例如， `getClass` 在上呼叫 `ArrayList<String>` 將不會傳回，而是不提供還原序列化程式 `Class<ArrayList<String>>` 足夠的資訊，以正確還原序列化 `Class<ArrayList>` 傳入訊息。</span><span class="sxs-lookup"><span data-stu-id="40a10-151">For example, calling `getClass` on an `ArrayList<String>` would not return `Class<ArrayList<String>>`, but rather `Class<ArrayList>`, which does not give the deserializer enough information to correctly deserialize an incoming message.</span></span> <span data-ttu-id="40a10-152">自訂物件也是如此。</span><span class="sxs-lookup"><span data-stu-id="40a10-152">The same is true for custom objects.</span></span>

::: moniker-end

## <a name="known-limitations"></a><span data-ttu-id="40a10-153">已知的限制</span><span class="sxs-lookup"><span data-stu-id="40a10-153">Known limitations</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="40a10-154">不支援傳輸回復和伺服器傳送的事件傳輸。</span><span class="sxs-lookup"><span data-stu-id="40a10-154">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* <span data-ttu-id="40a10-155">不支援傳輸回復和伺服器傳送的事件傳輸。</span><span class="sxs-lookup"><span data-stu-id="40a10-155">Transport fallback and the Server Sent Events transport aren't supported.</span></span>
* <span data-ttu-id="40a10-156">僅支援 JSON 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="40a10-156">Only the JSON protocol is supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="40a10-157">僅支援 JSON 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="40a10-157">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="40a10-158">僅支援 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="40a10-158">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="40a10-159">尚未支援串流。</span><span class="sxs-lookup"><span data-stu-id="40a10-159">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="40a10-160">其他資源</span><span class="sxs-lookup"><span data-stu-id="40a10-160">Additional resources</span></span>

* [<span data-ttu-id="40a10-161">Java API 參考</span><span class="sxs-lookup"><span data-stu-id="40a10-161">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="40a10-162">Azure SignalR 服務無伺服器檔</span><span class="sxs-lookup"><span data-stu-id="40a10-162">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
