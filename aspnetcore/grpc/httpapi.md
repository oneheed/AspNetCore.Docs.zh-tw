---
title: 從 gRPC 建立 JSON Web API
author: jamesnk
description: 瞭解如何建立 gRPC 服務的 JSON HTTP Api。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 64d18114e2fe9ee10edb902a98a281c3cd9f3393
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417574"
---
# <a name="create-json-web-apis-from-grpc"></a>從 gRPC 建立 JSON Web API

依 [James 牛頓](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API 是實驗性專案，不是認可的產品。 我們想要：
>
> * 測試我們為 gRPC 服務建立 JSON Web Api 的方法是否可運作。
> * 取得這種方法對 .NET 開發人員很有用的意見反應。
>
> 請 [留下意見](https://github.com/grpc/grpc-dotnet/issues/167) 反應，以確保我們建立了開發人員所喜愛的內容，並提高生產力。

gRPC 是在應用程式之間進行通訊的新式方法。 gRPC 使用 HTTP/2、串流、Protobuf 和訊息合約來建立高效能的即時服務。

GRPC 的其中一項限制不是每個平臺都可以使用它。 瀏覽器不完全支援 HTTP/2，讓 REST 和 JSON 成為將資料帶入瀏覽器應用程式的主要方式。 即使有 gRPC 帶來的好處，REST 和 JSON 在新式應用程式中都有一個重要的地方。 建立 gRPC ***和** _ JSON Web api 會在應用程式開發中增加不必要的額外負荷。

本檔討論如何使用 gRPC 服務來建立 JSON Web Api。

## <a name="grpc-http-api"></a>gRPC HTTP API

gRPC HTTP API 是 ASP.NET Core 的實驗性延伸模組，可建立 gRPC 服務的 RESTful JSON Api。 一旦設定之後，gRPC HTTP API 可讓應用程式使用熟悉的 HTTP 概念來呼叫 gRPC 服務：

_ HTTP 動詞命令
* URL 參數系結
* JSON 要求/回應

gRPC 仍然可以用來呼叫服務。

### <a name="usage"></a>使用方式

1. 將套件參考新增至 [AspNetCore. Grpc. HTTPapi.dll](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)。
1. 在 *Startup.cs* 中註冊服務 `AddGrpcHttpApi` 。
1. 將 [google/api/HTTP.sys](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) 和 [google/api/注釋. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) 檔案新增至您的專案。
1. 在您的 *proto* 檔案中使用 HTTP 系結和路由標注 gRPC 方法：

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

`SayHello`現在可將 gRPC 方法叫用為 gRPC + Protobuf 和 HTTP API：

* 要求： `HTTP/1.1 GET /v1/greeter/world`
* 回應︰ `{ "message": "Hello world" }`

伺服器記錄顯示 HTTP 呼叫是由 gRPC 服務所執行。 gRPC HTTP API 會將傳入 HTTP 要求對應至 gRPC 訊息，然後將回應訊息轉換為 JSON。

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

這是基本的範例。 如需更多自訂選項，請參閱 [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) 。

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API vs gRPC-Web

GRPC HTTP API 和 gRPC Web 都允許從瀏覽器呼叫 gRPC 服務。 不過，每個方法都不同：

* gRPC Web 可讓瀏覽器應用程式從瀏覽器使用 gRPC Web 用戶端和 Protobuf 來呼叫 gRPC services。 gRPC Web 需要瀏覽器應用程式產生 gRPC 用戶端，而且具有傳送小型、快速 Protobuf 訊息的優點。
* gRPC HTTP API 可讓瀏覽器應用程式呼叫 gRPC 服務，就像是使用 JSON RESTful Api 一樣。 瀏覽器應用程式不需要產生 gRPC 用戶端，也不需要知道有關 gRPC 的任何資訊。

未針對 gRPC HTTP API 建立任何產生的用戶端。 您 `Greeter` 可以使用瀏覽器 JavaScript api 來呼叫先前的服務：

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>實驗性狀態

gRPC HTTP API 是一項實驗。 它並不完整，而且不受支援。 我們對這項技術有興趣，並讓應用程式開發人員能夠同時快速建立 gRPC 和 JSON 服務。 沒有完成 gRPC HTTP API 的承諾。

我們想要測量 gRPC HTTP API 的開發人員興趣。 如果 gRPC HTTP API 對您感興趣，請 [提供意見](https://github.com/grpc/grpc-dotnet/issues/167)反應。

## <a name="grpc-gateway"></a>grpc-閘道

[grpc-閘道](https://grpc-ecosystem.github.io/grpc-gateway/) 是另一種技術，可從 grpc 服務建立 RESTFUL 的 JSON api。 它會使用相同的 *proto* 注釋，將 HTTP 概念對應至 gRPC 服務。

Grpc-閘道和 gRPC HTTP API 之間的最大差異是 grpc-閘道會使用程式碼產生來建立反向 proxy 伺服器。 反向 proxy 會將 RESTful 呼叫轉譯為 gRPC，然後將它們傳送至 gRPC 服務。

如需 grpc 閘道的安裝和使用方式，請參閱 [grpc-閘道讀我檔案](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway)。

## <a name="additional-resources"></a>其他資源

* [HttpRule 檔](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
