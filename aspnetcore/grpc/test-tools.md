---
title: 使用 gRPC 工具測試服務
author: jamesnk
description: 瞭解如何使用 gRPC 工具測試服務。 gRPCurl 可與 gRPC 服務互動的命令列工具。 gRPCui 是互動式的 web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594408"
---
# <a name="test-services-with-grpc-tools"></a>使用 gRPC 工具測試服務

依 [James 牛頓](https://twitter.com/jamesnk)

工具適用于 gRPC，可讓開發人員在不建立用戶端應用程式的情況下測試服務。 [gRPCurl](https://github.com/fullstorydev/grpcurl) 是一種命令列工具，可提供與 gRPC 服務的互動。 [gRPCui](https://github.com/fullstorydev/grpcui) 會新增適用于 gRPC 的互動式 web UI。

本文討論如何：

* 下載並安裝 gRPCurl 和 gRPCui。
* 使用 gRPC ASP.NET Core 應用程式設定 gRPC 反映。
* 使用探索和測試 gRPC 服務 `grpcurl` 。
* 使用瀏覽器透過瀏覽器與 gRPC 服務互動 `grpcui` 。

## <a name="about-grpcurl"></a>關於 gRPCurl

gRPCurl 是 gRPC 社區所建立的命令列工具。 其功能包括：

* 呼叫 gRPC 服務，包括串流服務。
* 使用 [gRPC 反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)的服務探索。
* 列出和描述 gRPC 服務。
* 適用于安全 (TLS) 和不安全的 (純文字) 伺服器。

如需下載和安裝的詳細資訊 `grpcurl` ，請參閱 [gRPCurl GitHub 首頁](https://github.com/fullstorydev/grpcurl#installation)。

## <a name="setup-grpc-reflection"></a>設定 gRPC 反映

`grpcurl` 需要知道服務的 Protobuf 合約，才能呼叫它們。 作法有二：

* 使用 gRPC 反映來探索服務合約。
* 在命令列引數中指定 *proto* 檔。

使用 gRPCurl 搭配 gRPC 反映和服務探索更加容易。 gRPC ASP.NET Core 內建支援 gRPC 反映和 [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 套件。 若要在應用程式中設定反映：

* 新增 `Grpc.AspNetCore.Server.Reflection` 封裝參考。
* 在 *Startup.cs*中註冊反映：
  * `AddGrpcReflection` 註冊啟用反映的服務。
  * `MapGrpcReflectionService` 以加入反映服務端點。

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>使用`grpcurl`

`-help`引數說明 `grpcurl` 命令列選項：

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>探索服務

使用 `describe` 動詞來查看伺服器定義的服務：

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

上述範例：

* `describe`在伺服器上執行 `localhost:5001` 動詞。
* 列印 gRPC 反映所傳回的服務和方法。
  * `Greeter` 是由應用程式所執行的服務。
  * `ServerReflection` 這是封裝所新增的服務 `Grpc.AspNetCore.Server.Reflection` 。

結合 `describe` 服務、方法或訊息名稱以查看其詳細資料：

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>呼叫 gRPC 服務

藉由指定服務和方法名稱，以及表示要求訊息的 JSON 引數，來呼叫 gRPC 服務。 JSON 會轉換成 Protobuf 並傳送至服務。

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

上述範例：

* `-d` 引數指定具有 JSON 的要求訊息。 此引數必須位於伺服器位址和方法名稱之前。
* 呼叫 `SayHello` 服務上的方法 `greeter.Greeter` 。
* 將回應訊息列印為 JSON。

## <a name="about-grpcui"></a>關於 gRPCui

gRPCui 是適用于 gRPC 的互動式 web UI。 它建置於 gRPCurl 之上，並提供用來探索和測試 gRPC 服務的 GUI，類似于像是 Postman 的 HTTP 工具。

如需下載和安裝的詳細資訊 `grpcui` ，請參閱 [gRPCui GitHub 首頁](https://github.com/fullstorydev/grpcui#installation)。

## <a name="using-grpcui"></a>使用 `grpcui`

`grpcui`以伺服器位址執行，以做為引數進行互動。

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

此工具將會啟動具有互動式 web UI 的瀏覽器視窗。 使用 gRPC 反映自動探索 gRPC 服務。

![gRPCui web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>其他資源

* [gRPCurl GitHub 首頁](https://github.com/fullstorydev/grpcurl)
* [gRPCui GitHub 首頁](https://github.com/fullstorydev/grpcui)
* [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
