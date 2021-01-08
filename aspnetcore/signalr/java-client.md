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
# <a name="aspnet-core-no-locsignalr-java-client"></a>ASP.NET Core SignalR JAVA 用戶端

依 [Mikael Mengistu](https://twitter.com/MikaelM_12)

JAVA 用戶端可 SignalR 從 java 程式碼（包括 Android 應用程式）連接到 ASP.NET Core 的伺服器。 JAVA 用戶端和 [JavaScript 客戶](xref:signalr/javascript-client) 端和 [.net 客戶](xref:signalr/dotnet-client)端一樣，可讓您即時接收和傳送訊息至中樞。 JAVA 用戶端可在 ASP.NET Core 2.2 和更新版本中使用。

本文中參考的 JAVA 主控台應用程式範例會使用 SignalR java 用戶端。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="install-the-no-locsignalr-java-client-package"></a>安裝 SignalR JAVA 用戶端套件

*Signalr 1.0.0* JAR 檔案可讓用戶端連接至 SignalR 中樞。 若要尋找最新的 JAR 檔案版本號碼，請參閱 [Maven 搜尋結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)。

如果使用 Gradle，請將下列這一行新增至 `dependencies` *Gradle* 檔案的區段：

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

如果使用 Maven，請將下列幾行新增至 `<dependencies>` *pom.xml* 檔案的元素內：

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>連接至中樞

若要建立 `HubConnection` ，則 `HubConnectionBuilder` 應該使用。 建立連接時，可以設定中樞 URL 和記錄層級。 在之前呼叫任何方法，以設定任何必要的選項 `HubConnectionBuilder` `build` 。 開始與的連接 `start` 。

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>從用戶端呼叫中樞方法

呼叫來叫 `send` 用中樞方法。 將中樞方法中定義的中樞方法名稱和任何引數傳遞至 `send` 。

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> 只有 SignalR 在 *預設* 模式下使用 Azure 服務時，才支援從用戶端呼叫中樞方法。 如需詳細資訊，請參閱 [ (azure Signalr GitHub 存放庫) ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)的常見問題。

## <a name="call-client-methods-from-hub"></a>從中樞呼叫用戶端方法

用 `hubConnection.on` 來定義用戶端上中樞可以呼叫的方法。 在建立之後，但在開始連接之前定義方法。

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>新增記錄

SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。 它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。 下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

這可以安全地忽略。

## <a name="android-development-notes"></a>Android 開發附注

在 Android SDK 用戶端功能的相容性方面 SignalR ，指定目標 Android SDK 版本時，請考慮下列專案：

* SignalRJAVA 用戶端會在 ANDROID API 層級16和更新版本上執行。
* 透過 Azure 服務連接 SignalR 將需要 ANDROID API 層級20和更新版本，因為 [azure SignalR 服務](/azure/azure-signalr/signalr-overview) 需要 TLS 1.2 且不支援 sha-1 型加密套件。 Android 在 API 層級20中 [新增了 SHA-256 (和以上) 加密套件的支援](https://developer.android.com/reference/javax/net/ssl/SSLSocket) 。

## <a name="configure-bearer-token-authentication"></a>設定持有人權杖驗證

在 SignalR JAVA 用戶端中，您可以將「存取權杖處理站」提供給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。 只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a>在 JAVA 中傳遞類別資訊

`on` `invoke` `stream` `HubConnection` 在 JAVA 用戶端中呼叫、或方法時，使用者應該傳遞 `Type` 物件而不是 `Class<?>` 物件，以描述任何傳遞給方法的泛型 `Object` 。 `Type`可以使用提供的 `TypeReference` 類別取得。 例如，使用名為的自訂泛型類別 `Foo<T>` ，下列程式碼會取得 `Type` ：

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

針對非泛型（例如基本類型或其他非參數化型別 `String` ），您可以直接使用內建 `.class` 。

使用一或多個物件類型呼叫其中一個方法時，請在叫用方法時使用泛型語法。 例如，註冊 `on` 名為之方法的處理常式時 `func` ，會以字串和物件做為引數 `Foo<String>` ，請使用下列程式碼來設定列印引數的動作：

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

這是必要的慣例，因為我們無法使用方法來取得複雜類型的完整資訊，因為 `Object.getClass` JAVA 中的型別抹除。 例如， `getClass` 在上呼叫 `ArrayList<String>` 將不會傳回，而是不提供還原序列化程式 `Class<ArrayList<String>>` 足夠的資訊，以正確還原序列化 `Class<ArrayList>` 傳入訊息。 自訂物件也是如此。

::: moniker-end

## <a name="known-limitations"></a>已知的限制

::: moniker range=">= aspnetcore-5.0"

* 不支援傳輸回復和伺服器傳送的事件傳輸。

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* 不支援傳輸回復和伺服器傳送的事件傳輸。
* 僅支援 JSON 通訊協定。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 僅支援 JSON 通訊協定。
* 僅支援 Websocket 傳輸。
* 尚未支援串流。

::: moniker-end

## <a name="additional-resources"></a>其他資源

* [Java API 參考](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR 服務無伺服器檔](/azure/azure-signalr/signalr-concept-serverless-development-config)
