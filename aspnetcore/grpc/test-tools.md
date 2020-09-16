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
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="ccf38-105">使用 gRPC 工具測試服務</span><span class="sxs-lookup"><span data-stu-id="ccf38-105">Test services with gRPC tools</span></span>

<span data-ttu-id="ccf38-106">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ccf38-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="ccf38-107">工具適用于 gRPC，可讓開發人員在不建立用戶端應用程式的情況下測試服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="ccf38-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) 是一種命令列工具，可提供與 gRPC 服務的互動。</span><span class="sxs-lookup"><span data-stu-id="ccf38-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="ccf38-109">[gRPCui](https://github.com/fullstorydev/grpcui) 會新增適用于 gRPC 的互動式 web UI。</span><span class="sxs-lookup"><span data-stu-id="ccf38-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="ccf38-110">本文討論如何：</span><span class="sxs-lookup"><span data-stu-id="ccf38-110">This article discusses how to:</span></span>

* <span data-ttu-id="ccf38-111">下載並安裝 gRPCurl 和 gRPCui。</span><span class="sxs-lookup"><span data-stu-id="ccf38-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="ccf38-112">使用 gRPC ASP.NET Core 應用程式設定 gRPC 反映。</span><span class="sxs-lookup"><span data-stu-id="ccf38-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="ccf38-113">使用探索和測試 gRPC 服務 `grpcurl` 。</span><span class="sxs-lookup"><span data-stu-id="ccf38-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="ccf38-114">使用瀏覽器透過瀏覽器與 gRPC 服務互動 `grpcui` 。</span><span class="sxs-lookup"><span data-stu-id="ccf38-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="ccf38-115">關於 gRPCurl</span><span class="sxs-lookup"><span data-stu-id="ccf38-115">About gRPCurl</span></span>

<span data-ttu-id="ccf38-116">gRPCurl 是 gRPC 社區所建立的命令列工具。</span><span class="sxs-lookup"><span data-stu-id="ccf38-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="ccf38-117">其功能包括：</span><span class="sxs-lookup"><span data-stu-id="ccf38-117">Its features include:</span></span>

* <span data-ttu-id="ccf38-118">呼叫 gRPC 服務，包括串流服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="ccf38-119">使用 [gRPC 反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)的服務探索。</span><span class="sxs-lookup"><span data-stu-id="ccf38-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="ccf38-120">列出和描述 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="ccf38-121">適用于安全 (TLS) 和不安全的 (純文字) 伺服器。</span><span class="sxs-lookup"><span data-stu-id="ccf38-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="ccf38-122">如需下載和安裝的詳細資訊 `grpcurl` ，請參閱 [gRPCurl GitHub 首頁](https://github.com/fullstorydev/grpcurl#installation)。</span><span class="sxs-lookup"><span data-stu-id="ccf38-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="ccf38-123">設定 gRPC 反映</span><span class="sxs-lookup"><span data-stu-id="ccf38-123">Setup gRPC reflection</span></span>

<span data-ttu-id="ccf38-124">`grpcurl` 需要知道服務的 Protobuf 合約，才能呼叫它們。</span><span class="sxs-lookup"><span data-stu-id="ccf38-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="ccf38-125">作法有二：</span><span class="sxs-lookup"><span data-stu-id="ccf38-125">There are two ways to do this:</span></span>

* <span data-ttu-id="ccf38-126">使用 gRPC 反映來探索服務合約。</span><span class="sxs-lookup"><span data-stu-id="ccf38-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="ccf38-127">在命令列引數中指定 *proto* 檔。</span><span class="sxs-lookup"><span data-stu-id="ccf38-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="ccf38-128">使用 gRPCurl 搭配 gRPC 反映和服務探索更加容易。</span><span class="sxs-lookup"><span data-stu-id="ccf38-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="ccf38-129">gRPC ASP.NET Core 內建支援 gRPC 反映和 [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 套件。</span><span class="sxs-lookup"><span data-stu-id="ccf38-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="ccf38-130">若要在應用程式中設定反映：</span><span class="sxs-lookup"><span data-stu-id="ccf38-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="ccf38-131">新增 `Grpc.AspNetCore.Server.Reflection` 封裝參考。</span><span class="sxs-lookup"><span data-stu-id="ccf38-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="ccf38-132">在 *Startup.cs*中註冊反映：</span><span class="sxs-lookup"><span data-stu-id="ccf38-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="ccf38-133">`AddGrpcReflection` 註冊啟用反映的服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="ccf38-134">`MapGrpcReflectionService` 以加入反映服務端點。</span><span class="sxs-lookup"><span data-stu-id="ccf38-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="ccf38-135">使用`grpcurl`</span><span class="sxs-lookup"><span data-stu-id="ccf38-135">Use `grpcurl`</span></span>

<span data-ttu-id="ccf38-136">`-help`引數說明 `grpcurl` 命令列選項：</span><span class="sxs-lookup"><span data-stu-id="ccf38-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="ccf38-137">探索服務</span><span class="sxs-lookup"><span data-stu-id="ccf38-137">Discover services</span></span>

<span data-ttu-id="ccf38-138">使用 `describe` 動詞來查看伺服器定義的服務：</span><span class="sxs-lookup"><span data-stu-id="ccf38-138">Use the `describe` verb to view the services defined by the server:</span></span>

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

<span data-ttu-id="ccf38-139">上述範例：</span><span class="sxs-lookup"><span data-stu-id="ccf38-139">The preceding example:</span></span>

* <span data-ttu-id="ccf38-140">`describe`在伺服器上執行 `localhost:5001` 動詞。</span><span class="sxs-lookup"><span data-stu-id="ccf38-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="ccf38-141">列印 gRPC 反映所傳回的服務和方法。</span><span class="sxs-lookup"><span data-stu-id="ccf38-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="ccf38-142">`Greeter` 是由應用程式所執行的服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="ccf38-143">`ServerReflection` 這是封裝所新增的服務 `Grpc.AspNetCore.Server.Reflection` 。</span><span class="sxs-lookup"><span data-stu-id="ccf38-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="ccf38-144">結合 `describe` 服務、方法或訊息名稱以查看其詳細資料：</span><span class="sxs-lookup"><span data-stu-id="ccf38-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="ccf38-145">呼叫 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="ccf38-145">Call gRPC services</span></span>

<span data-ttu-id="ccf38-146">藉由指定服務和方法名稱，以及表示要求訊息的 JSON 引數，來呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="ccf38-147">JSON 會轉換成 Protobuf 並傳送至服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="ccf38-148">上述範例：</span><span class="sxs-lookup"><span data-stu-id="ccf38-148">The preceding example:</span></span>

* <span data-ttu-id="ccf38-149">`-d` 引數指定具有 JSON 的要求訊息。</span><span class="sxs-lookup"><span data-stu-id="ccf38-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="ccf38-150">此引數必須位於伺服器位址和方法名稱之前。</span><span class="sxs-lookup"><span data-stu-id="ccf38-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="ccf38-151">呼叫 `SayHello` 服務上的方法 `greeter.Greeter` 。</span><span class="sxs-lookup"><span data-stu-id="ccf38-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="ccf38-152">將回應訊息列印為 JSON。</span><span class="sxs-lookup"><span data-stu-id="ccf38-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="ccf38-153">關於 gRPCui</span><span class="sxs-lookup"><span data-stu-id="ccf38-153">About gRPCui</span></span>

<span data-ttu-id="ccf38-154">gRPCui 是適用于 gRPC 的互動式 web UI。</span><span class="sxs-lookup"><span data-stu-id="ccf38-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="ccf38-155">它建置於 gRPCurl 之上，並提供用來探索和測試 gRPC 服務的 GUI，類似于像是 Postman 的 HTTP 工具。</span><span class="sxs-lookup"><span data-stu-id="ccf38-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="ccf38-156">如需下載和安裝的詳細資訊 `grpcui` ，請參閱 [gRPCui GitHub 首頁](https://github.com/fullstorydev/grpcui#installation)。</span><span class="sxs-lookup"><span data-stu-id="ccf38-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="ccf38-157">使用 `grpcui`</span><span class="sxs-lookup"><span data-stu-id="ccf38-157">Using `grpcui`</span></span>

<span data-ttu-id="ccf38-158">`grpcui`以伺服器位址執行，以做為引數進行互動。</span><span class="sxs-lookup"><span data-stu-id="ccf38-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="ccf38-159">此工具將會啟動具有互動式 web UI 的瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="ccf38-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="ccf38-160">使用 gRPC 反映自動探索 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="ccf38-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="ccf38-162">其他資源</span><span class="sxs-lookup"><span data-stu-id="ccf38-162">Additional resources</span></span>

* [<span data-ttu-id="ccf38-163">gRPCurl GitHub 首頁</span><span class="sxs-lookup"><span data-stu-id="ccf38-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="ccf38-164">gRPCui GitHub 首頁</span><span class="sxs-lookup"><span data-stu-id="ccf38-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="ccf38-165">Grpc. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="ccf38-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
