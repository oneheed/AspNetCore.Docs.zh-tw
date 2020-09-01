---
title: 從 gRPC 建立 JSON Web Api
author: jamesnk
description: 瞭解如何建立 gRPC 服務的 JSON HTTP Api。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102886"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="14458-103">從 gRPC 建立 JSON Web Api</span><span class="sxs-lookup"><span data-stu-id="14458-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="14458-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="14458-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="14458-105">gRPC HTTP API 是實驗性專案，不是認可的產品。</span><span class="sxs-lookup"><span data-stu-id="14458-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="14458-106">我們想要：</span><span class="sxs-lookup"><span data-stu-id="14458-106">We want to:</span></span>
>
> * <span data-ttu-id="14458-107">測試我們為 gRPC 服務建立 JSON Web Api 的方法是否可運作。</span><span class="sxs-lookup"><span data-stu-id="14458-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="14458-108">取得這種方法對 .NET 開發人員很有用的意見反應。</span><span class="sxs-lookup"><span data-stu-id="14458-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="14458-109">請 [留下意見](https://github.com/grpc/grpc-dotnet/issues/167) 反應，以確保我們建立了開發人員所喜愛的內容，並提高生產力。</span><span class="sxs-lookup"><span data-stu-id="14458-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="14458-110">gRPC 是在應用程式之間進行通訊的新式方法。</span><span class="sxs-lookup"><span data-stu-id="14458-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="14458-111">gRPC 使用 HTTP/2、串流、Protobuf 和訊息合約來建立高效能的即時服務。</span><span class="sxs-lookup"><span data-stu-id="14458-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="14458-112">GRPC 的其中一項限制不是每個平臺都可以使用它。</span><span class="sxs-lookup"><span data-stu-id="14458-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="14458-113">瀏覽器不完全支援 HTTP/2，讓 REST 和 JSON 成為將資料帶入瀏覽器應用程式的主要方式。</span><span class="sxs-lookup"><span data-stu-id="14458-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="14458-114">即使有 gRPC 帶來的好處，REST 和 JSON 在新式應用程式中都有一個重要的地方。</span><span class="sxs-lookup"><span data-stu-id="14458-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="14458-115">建立 gRPC ***和*** JSON Web api 會在應用程式開發中增加不必要的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="14458-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="14458-116">本檔討論如何使用 gRPC 服務來建立 JSON Web Api。</span><span class="sxs-lookup"><span data-stu-id="14458-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="14458-117">gRPC HTTP API</span><span class="sxs-lookup"><span data-stu-id="14458-117">gRPC HTTP API</span></span>

<span data-ttu-id="14458-118">gRPC HTTP API 是 ASP.NET Core 的實驗性延伸模組，可建立 gRPC 服務的 RESTful JSON Api。</span><span class="sxs-lookup"><span data-stu-id="14458-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="14458-119">一旦設定之後，gRPC HTTP API 可讓應用程式使用熟悉的 HTTP 概念來呼叫 gRPC 服務：</span><span class="sxs-lookup"><span data-stu-id="14458-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="14458-120">HTTP 動詞</span><span class="sxs-lookup"><span data-stu-id="14458-120">HTTP verbs</span></span>
* <span data-ttu-id="14458-121">URL 參數系結</span><span class="sxs-lookup"><span data-stu-id="14458-121">URL parameter binding</span></span>
* <span data-ttu-id="14458-122">JSON 要求/回應</span><span class="sxs-lookup"><span data-stu-id="14458-122">JSON requests/responses</span></span>

<span data-ttu-id="14458-123">gRPC 仍然可以用來呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="14458-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="14458-124">使用方式</span><span class="sxs-lookup"><span data-stu-id="14458-124">Usage</span></span>

1. <span data-ttu-id="14458-125">將套件參考新增至 [AspNetCore. Grpc. HTTPapi.dll](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)。</span><span class="sxs-lookup"><span data-stu-id="14458-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="14458-126">在 *Startup.cs* 中註冊服務 `AddGrpcHttpApi` 。</span><span class="sxs-lookup"><span data-stu-id="14458-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="14458-127">將 [google/api/HTTP.sys](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) 和 [google/api/注釋. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) 檔案新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="14458-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="14458-128">在您的 *proto* 檔案中使用 HTTP 系結和路由標注 gRPC 方法：</span><span class="sxs-lookup"><span data-stu-id="14458-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
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

<span data-ttu-id="14458-129">`SayHello`現在可將 gRPC 方法叫用為 gRPC + Protobuf 和 HTTP API：</span><span class="sxs-lookup"><span data-stu-id="14458-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="14458-130">要求： `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="14458-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="14458-131">回應︰ `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="14458-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="14458-132">伺服器記錄顯示 HTTP 呼叫是由 gRPC 服務所執行。</span><span class="sxs-lookup"><span data-stu-id="14458-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="14458-133">gRPC HTTP API 會將傳入 HTTP 要求對應至 gRPC 訊息，然後將回應訊息轉換為 JSON。</span><span class="sxs-lookup"><span data-stu-id="14458-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="14458-134">這是基本的範例。</span><span class="sxs-lookup"><span data-stu-id="14458-134">This is a basic example.</span></span> <span data-ttu-id="14458-135">如需更多自訂選項，請參閱 [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) 。</span><span class="sxs-lookup"><span data-stu-id="14458-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="14458-136">gRPC HTTP API vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="14458-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="14458-137">GRPC HTTP API 和 gRPC Web 都允許從瀏覽器呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="14458-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="14458-138">不過，每個方法都不同：</span><span class="sxs-lookup"><span data-stu-id="14458-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="14458-139">gRPC Web 可讓瀏覽器應用程式從瀏覽器使用 gRPC Web 用戶端和 Protobuf 來呼叫 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="14458-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="14458-140">gRPC Web 需要瀏覽器應用程式產生 gRPC 用戶端，而且具有傳送小型、快速 Protobuf 訊息的優點。</span><span class="sxs-lookup"><span data-stu-id="14458-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="14458-141">gRPC HTTP API 可讓瀏覽器應用程式呼叫 gRPC 服務，就像是使用 JSON RESTful Api 一樣。</span><span class="sxs-lookup"><span data-stu-id="14458-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="14458-142">瀏覽器應用程式不需要產生 gRPC 用戶端，也不需要知道有關 gRPC 的任何資訊。</span><span class="sxs-lookup"><span data-stu-id="14458-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="14458-143">未針對 gRPC HTTP API 建立任何產生的用戶端。</span><span class="sxs-lookup"><span data-stu-id="14458-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="14458-144">您 `Greeter` 可以使用瀏覽器 JavaScript api 來呼叫先前的服務：</span><span class="sxs-lookup"><span data-stu-id="14458-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="14458-145">實驗性狀態</span><span class="sxs-lookup"><span data-stu-id="14458-145">Experimental status</span></span>

<span data-ttu-id="14458-146">gRPC HTTP API 是一項實驗。</span><span class="sxs-lookup"><span data-stu-id="14458-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="14458-147">它並不完整，而且不受支援。</span><span class="sxs-lookup"><span data-stu-id="14458-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="14458-148">我們對這項技術有興趣，並讓應用程式開發人員能夠同時快速建立 gRPC 和 JSON 服務。</span><span class="sxs-lookup"><span data-stu-id="14458-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="14458-149">沒有完成 gRPC HTTP API 的承諾。</span><span class="sxs-lookup"><span data-stu-id="14458-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="14458-150">我們想要測量 gRPC HTTP API 的開發人員興趣。</span><span class="sxs-lookup"><span data-stu-id="14458-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="14458-151">如果 gRPC HTTP API 對您感興趣，請 [提供意見](https://github.com/grpc/grpc-dotnet/issues/167)反應。</span><span class="sxs-lookup"><span data-stu-id="14458-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="14458-152">grpc-閘道</span><span class="sxs-lookup"><span data-stu-id="14458-152">grpc-gateway</span></span>

<span data-ttu-id="14458-153">[grpc-閘道](https://grpc-ecosystem.github.io/grpc-gateway/) 是另一種技術，可從 grpc 服務建立 RESTFUL 的 JSON api。</span><span class="sxs-lookup"><span data-stu-id="14458-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="14458-154">它會使用相同的 *proto* 注釋，將 HTTP 概念對應至 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="14458-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="14458-155">Grpc-閘道和 gRPC HTTP API 之間的最大差異是 grpc-閘道會使用程式碼產生來建立反向 proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="14458-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="14458-156">反向 proxy 會將 RESTful 呼叫轉譯為 gRPC，然後將它們傳送至 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="14458-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="14458-157">如需 grpc 閘道的安裝和使用方式，請參閱 [grpc-閘道檔](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html)。</span><span class="sxs-lookup"><span data-stu-id="14458-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14458-158">其他資源</span><span class="sxs-lookup"><span data-stu-id="14458-158">Additional resources</span></span>

* [<span data-ttu-id="14458-159">HttpRule 檔</span><span class="sxs-lookup"><span data-stu-id="14458-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
