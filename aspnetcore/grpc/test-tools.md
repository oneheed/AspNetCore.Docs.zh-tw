---
title: 在 ASP.NET Core 中使用 gRPCurl 測試 gRPC 服務
author: jamesnk
description: 瞭解如何使用 gRPC 工具測試服務。 gRPCurl 可與 gRPC 服務互動的命令列工具。 gRPCui 是互動式的 web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058788"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="99bc0-105">在 ASP.NET Core 中使用 gRPCurl 測試 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="99bc0-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="99bc0-106">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="99bc0-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="99bc0-107">工具適用于 gRPC，可讓開發人員在不建立用戶端應用程式的情況下測試服務：</span><span class="sxs-lookup"><span data-stu-id="99bc0-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="99bc0-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) 是一種命令列工具，可提供與 gRPC 服務的互動。</span><span class="sxs-lookup"><span data-stu-id="99bc0-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="99bc0-109">[gRPCui](https://github.com/fullstorydev/grpcui) 建置於 gRPCurl 之上，並新增適用于 gRPC 的互動式 web UI，類似于 Postman 和 Swagger UI 之類的工具。</span><span class="sxs-lookup"><span data-stu-id="99bc0-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="99bc0-110">本文討論如何：</span><span class="sxs-lookup"><span data-stu-id="99bc0-110">This article discusses how to:</span></span>

* <span data-ttu-id="99bc0-111">下載並安裝 gRPCurl 和 gRPCui。</span><span class="sxs-lookup"><span data-stu-id="99bc0-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="99bc0-112">使用 gRPC ASP.NET Core 應用程式來設定 gRPC 反映。</span><span class="sxs-lookup"><span data-stu-id="99bc0-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="99bc0-113">使用探索和測試 gRPC 服務 `grpcurl` 。</span><span class="sxs-lookup"><span data-stu-id="99bc0-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="99bc0-114">使用瀏覽器透過瀏覽器與 gRPC 服務互動 `grpcui` 。</span><span class="sxs-lookup"><span data-stu-id="99bc0-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="99bc0-115">關於 gRPCurl</span><span class="sxs-lookup"><span data-stu-id="99bc0-115">About gRPCurl</span></span>

<span data-ttu-id="99bc0-116">gRPCurl 是 gRPC 社區所建立的命令列工具。</span><span class="sxs-lookup"><span data-stu-id="99bc0-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="99bc0-117">其功能包括：</span><span class="sxs-lookup"><span data-stu-id="99bc0-117">Its features include:</span></span>

* <span data-ttu-id="99bc0-118">呼叫 gRPC 服務，包括串流服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="99bc0-119">使用 [gRPC 反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)的服務探索。</span><span class="sxs-lookup"><span data-stu-id="99bc0-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="99bc0-120">列出和描述 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="99bc0-121">適用于安全 (TLS) 和不安全的 (純文字) 伺服器。</span><span class="sxs-lookup"><span data-stu-id="99bc0-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="99bc0-122">如需下載和安裝的詳細資訊 `grpcurl` ，請參閱 [gRPCurl GitHub 首頁](https://github.com/fullstorydev/grpcurl#installation)。</span><span class="sxs-lookup"><span data-stu-id="99bc0-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![gRPCurl 命令列](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="99bc0-124">設定 gRPC 反映</span><span class="sxs-lookup"><span data-stu-id="99bc0-124">Set up gRPC reflection</span></span>

<span data-ttu-id="99bc0-125">`grpcurl` 必須知道服務的 Protobuf 合約，才能呼叫它們。</span><span class="sxs-lookup"><span data-stu-id="99bc0-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="99bc0-126">作法有二：</span><span class="sxs-lookup"><span data-stu-id="99bc0-126">There are two ways to do this:</span></span>

* <span data-ttu-id="99bc0-127">在伺服器上設定 [gRPC 反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 。</span><span class="sxs-lookup"><span data-stu-id="99bc0-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="99bc0-128">gRPCurl 會自動探索服務合約。</span><span class="sxs-lookup"><span data-stu-id="99bc0-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="99bc0-129">`.proto`在命令列引數中指定要 gRPCurl 的檔案。</span><span class="sxs-lookup"><span data-stu-id="99bc0-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="99bc0-130">使用 gRPCurl 搭配 gRPC 反映更容易。</span><span class="sxs-lookup"><span data-stu-id="99bc0-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="99bc0-131">gRPC 反映會將新的 gRPC 服務新增至用戶端可以呼叫以探索服務的應用程式。</span><span class="sxs-lookup"><span data-stu-id="99bc0-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="99bc0-132">gRPC ASP.NET Core 內建支援 gRPC 反映與 [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 封裝。</span><span class="sxs-lookup"><span data-stu-id="99bc0-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="99bc0-133">若要在應用程式中設定反映：</span><span class="sxs-lookup"><span data-stu-id="99bc0-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="99bc0-134">加入 `Grpc.AspNetCore.Server.Reflection` 封裝參考。</span><span class="sxs-lookup"><span data-stu-id="99bc0-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="99bc0-135">在下列情況中註冊反映 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="99bc0-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="99bc0-136">`AddGrpcReflection` 註冊啟用反映的服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="99bc0-137">`MapGrpcReflectionService` 新增反映服務端點。</span><span class="sxs-lookup"><span data-stu-id="99bc0-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="99bc0-138">設定 gRPC 反映時：</span><span class="sxs-lookup"><span data-stu-id="99bc0-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="99bc0-139">GRPC 反映服務會新增至伺服器應用程式。</span><span class="sxs-lookup"><span data-stu-id="99bc0-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="99bc0-140">支援 gRPC 反映的用戶端應用程式可以呼叫反映服務，以探索伺服器所主控的服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="99bc0-141">gRPC 服務仍從用戶端呼叫。</span><span class="sxs-lookup"><span data-stu-id="99bc0-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="99bc0-142">反映只會啟用服務探索，且不會略過伺服器端安全性。</span><span class="sxs-lookup"><span data-stu-id="99bc0-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="99bc0-143">由 [驗證和授權](xref:grpc/authn-and-authz) 所保護的端點，要求呼叫端必須傳遞要成功呼叫端點的認證。</span><span class="sxs-lookup"><span data-stu-id="99bc0-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="99bc0-144">使用`grpcurl`</span><span class="sxs-lookup"><span data-stu-id="99bc0-144">Use `grpcurl`</span></span>

<span data-ttu-id="99bc0-145">`-help`引數說明 `grpcurl` 命令列選項：</span><span class="sxs-lookup"><span data-stu-id="99bc0-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="99bc0-146">探索服務</span><span class="sxs-lookup"><span data-stu-id="99bc0-146">Discover services</span></span>

<span data-ttu-id="99bc0-147">使用 `describe` 動詞來查看伺服器定義的服務：</span><span class="sxs-lookup"><span data-stu-id="99bc0-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
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

<span data-ttu-id="99bc0-148">上述範例：</span><span class="sxs-lookup"><span data-stu-id="99bc0-148">The preceding example:</span></span>

* <span data-ttu-id="99bc0-149">`describe`在伺服器上執行指令動詞 `localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="99bc0-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="99bc0-150">列印 gRPC 反映所傳回的服務和方法。</span><span class="sxs-lookup"><span data-stu-id="99bc0-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="99bc0-151">`Greeter` 是由應用程式所執行的服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="99bc0-152">`ServerReflection` 這是封裝所新增的服務 `Grpc.AspNetCore.Server.Reflection` 。</span><span class="sxs-lookup"><span data-stu-id="99bc0-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="99bc0-153">結合 `describe` 服務、方法或訊息名稱以查看其詳細資料：</span><span class="sxs-lookup"><span data-stu-id="99bc0-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="99bc0-154">呼叫 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="99bc0-154">Call gRPC services</span></span>

<span data-ttu-id="99bc0-155">藉由指定服務和方法名稱以及表示要求訊息的 JSON 引數，來呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="99bc0-156">JSON 會轉換成 Protobuf 並傳送至服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="99bc0-157">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="99bc0-157">In the preceding example:</span></span>

* <span data-ttu-id="99bc0-158">`-d`引數指定具有 JSON 的要求訊息。</span><span class="sxs-lookup"><span data-stu-id="99bc0-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="99bc0-159">此引數必須位於伺服器位址和方法名稱之前。</span><span class="sxs-lookup"><span data-stu-id="99bc0-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="99bc0-160">呼叫 `SayHello` 服務上的方法 `greeter.Greeter` 。</span><span class="sxs-lookup"><span data-stu-id="99bc0-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="99bc0-161">將回應訊息列印為 JSON。</span><span class="sxs-lookup"><span data-stu-id="99bc0-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="99bc0-162">關於 gRPCui</span><span class="sxs-lookup"><span data-stu-id="99bc0-162">About gRPCui</span></span>

<span data-ttu-id="99bc0-163">gRPCui 是適用于 gRPC 的互動式 web UI。</span><span class="sxs-lookup"><span data-stu-id="99bc0-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="99bc0-164">它建置於 gRPCurl 之上，並提供用來探索和測試 gRPC 服務的 GUI，類似于 HTTP 工具，例如 Postman 或 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="99bc0-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="99bc0-165">如需下載和安裝的詳細資訊 `grpcui` ，請參閱 [gRPCui GitHub 首頁](https://github.com/fullstorydev/grpcui#installation)。</span><span class="sxs-lookup"><span data-stu-id="99bc0-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="99bc0-166">使用 `grpcui`</span><span class="sxs-lookup"><span data-stu-id="99bc0-166">Using `grpcui`</span></span>

<span data-ttu-id="99bc0-167">以 `grpcui` 伺服器位址執行以做為引數進行互動：</span><span class="sxs-lookup"><span data-stu-id="99bc0-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="99bc0-168">此工具會使用互動式 web UI 來啟動瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="99bc0-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="99bc0-169">使用 gRPC 反映自動探索 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="99bc0-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="99bc0-171">其他資源</span><span class="sxs-lookup"><span data-stu-id="99bc0-171">Additional resources</span></span>

* [<span data-ttu-id="99bc0-172">gRPCurl GitHub 首頁</span><span class="sxs-lookup"><span data-stu-id="99bc0-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="99bc0-173">gRPCui GitHub 首頁</span><span class="sxs-lookup"><span data-stu-id="99bc0-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
