---
title: 在 ASP.NET Core 中使用 gRPCurl 測試 gRPC 服務
author: jamesnk
description: 瞭解如何使用 gRPC 工具測試服務。 gRPCurl 可與 gRPC 服務互動的命令列工具。 gRPCui 是互動式的 web UI。
monikerRange: '>= aspnetcore-3.1'
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
ms.openlocfilehash: 15652431ea4bebc879af4c57667cbf854c49330c
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721812"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>在 ASP.NET Core 中使用 gRPCurl 測試 gRPC 服務

依 [James 牛頓](https://twitter.com/jamesnk)

工具適用于 gRPC，可讓開發人員在不建立用戶端應用程式的情況下測試服務：

* [gRPCurl](https://github.com/fullstorydev/grpcurl) 是一種命令列工具，可提供與 gRPC 服務的互動。
* [gRPCui](https://github.com/fullstorydev/grpcui) 建置於 gRPCurl 之上，並新增適用于 gRPC 的互動式 web UI，類似于 Postman 和 Swagger UI 之類的工具。

本文討論如何：

* 下載並安裝 gRPCurl 和 gRPCui。
* 使用 gRPC ASP.NET Core 應用程式來設定 gRPC 反映。
* 使用探索和測試 gRPC 服務 `grpcurl` 。
* 使用瀏覽器透過瀏覽器與 gRPC 服務互動 `grpcui` 。

## <a name="about-grpcurl"></a>關於 gRPCurl

gRPCurl 是 gRPC 社區所建立的命令列工具。 其功能包括：

* 呼叫 gRPC 服務，包括串流服務。
* 使用 [gRPC 反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)的服務探索。
* 列出和描述 gRPC 服務。
* 適用于安全 (TLS) 和不安全的 (純文字) 伺服器。

如需下載和安裝的詳細資訊 `grpcurl` ，請參閱 [gRPCurl GitHub 首頁](https://github.com/fullstorydev/grpcurl#installation)。

![gRPCurl 命令列](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>設定 gRPC 反映

`grpcurl` 必須知道服務的 Protobuf 合約，才能呼叫它們。 作法有二：

* 在伺服器上設定 [gRPC 反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 。 gRPCurl 會自動探索服務合約。
* `.proto`在命令列引數中指定要 gRPCurl 的檔案。

使用 gRPCurl 搭配 gRPC 反映更容易。 gRPC 反映會將新的 gRPC 服務新增至用戶端可以呼叫以探索服務的應用程式。

gRPC ASP.NET Core 內建支援 gRPC 反映與 [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 封裝。 若要在應用程式中設定反映：

* 加入 `Grpc.AspNetCore.Server.Reflection` 封裝參考。
* 在下列情況中註冊反映 `Startup.cs` ：
  * `AddGrpcReflection` 註冊啟用反映的服務。
  * `MapGrpcReflectionService` 新增反映服務端點。

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

設定 gRPC 反映時：

* GRPC 反映服務會新增至伺服器應用程式。
* 支援 gRPC 反映的用戶端應用程式可以呼叫反映服務，以探索伺服器所主控的服務。
* gRPC 服務仍從用戶端呼叫。 反映只會啟用服務探索，且不會略過伺服器端安全性。 由 [驗證和授權](xref:grpc/authn-and-authz) 所保護的端點，要求呼叫端必須傳遞要成功呼叫端點的認證。

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

* `describe`在伺服器上執行指令動詞 `localhost:5001` 。
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

藉由指定服務和方法名稱以及表示要求訊息的 JSON 引數，來呼叫 gRPC 服務。 JSON 會轉換成 Protobuf 並傳送至服務。

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

在上述範例中：

* `-d`引數指定具有 JSON 的要求訊息。 此引數必須位於伺服器位址和方法名稱之前。
* 呼叫 `SayHello` 服務上的方法 `greeter.Greeter` 。
* 將回應訊息列印為 JSON。

## <a name="about-grpcui"></a>關於 gRPCui

gRPCui 是適用于 gRPC 的互動式 web UI。 它建置於 gRPCurl 之上，並提供用來探索和測試 gRPC 服務的 GUI，類似于 HTTP 工具，例如 Postman 或 Swagger UI。

如需下載和安裝的詳細資訊 `grpcui` ，請參閱 [gRPCui GitHub 首頁](https://github.com/fullstorydev/grpcui#installation)。

## <a name="using-grpcui"></a>使用 `grpcui`

以 `grpcui` 伺服器位址執行以做為引數進行互動：

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

此工具會使用互動式 web UI 來啟動瀏覽器視窗。 使用 gRPC 反映自動探索 gRPC 服務。

![gRPCui web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>其他資源

* [gRPCurl GitHub 首頁](https://github.com/fullstorydev/grpcurl)
* [gRPCui GitHub 首頁](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
