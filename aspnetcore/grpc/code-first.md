---
title: 使用 .NET 的 Code first gRPC 服務和用戶端
author: jamesnk
description: 瞭解使用 .NET 撰寫程式碼優先 gRPC 時的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880630"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="98185-103">使用 .NET 的 Code first gRPC 服務和用戶端</span><span class="sxs-lookup"><span data-stu-id="98185-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="98185-104">依 [James 牛頓](https://twitter.com/jamesnk) 和 [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="98185-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="98185-105">程式碼優先 gRPC 使用 .NET 類型來定義服務和訊息合約。</span><span class="sxs-lookup"><span data-stu-id="98185-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="98185-106">當整個系統使用 .NET 時，程式碼優先是不錯的選擇：</span><span class="sxs-lookup"><span data-stu-id="98185-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="98185-107">.Net 服務和資料合約類型可在 .NET 伺服器和用戶端之間共用。</span><span class="sxs-lookup"><span data-stu-id="98185-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="98185-108">避免需要在檔案和程式碼產生中定義合約 `.proto` 。</span><span class="sxs-lookup"><span data-stu-id="98185-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="98185-109">在具有多種語言的多語言系統中，不建議使用 Code first。</span><span class="sxs-lookup"><span data-stu-id="98185-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="98185-110">.NET 服務和資料合約類型無法與 non-.NET 平臺搭配使用。</span><span class="sxs-lookup"><span data-stu-id="98185-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="98185-111">若要呼叫使用 code first 撰寫的 gRPC 服務，其他平臺必須建立 `.proto` 符合服務的合約。</span><span class="sxs-lookup"><span data-stu-id="98185-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="98185-112">protobuf-net。Grpc</span><span class="sxs-lookup"><span data-stu-id="98185-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="98185-113">如需 protobuf 的協助。Grpc，請造訪 [protobuf-網路。Grpc 網站](https://protobuf-net.github.io/protobuf-net.Grpc/) ，或在 protobuf 網路上建立問題 [。Grpc GitHub 存放庫](https://github.com/protobuf-net/protobuf-net.Grpc)。</span><span class="sxs-lookup"><span data-stu-id="98185-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="98185-114">[protobuf-net。Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) 是一個小組專案，Microsoft 並不支援。</span><span class="sxs-lookup"><span data-stu-id="98185-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="98185-115">它會將程式碼優先支援新增至 `Grpc.AspNetCore` 和 `Grpc.Net.Client` 。</span><span class="sxs-lookup"><span data-stu-id="98185-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="98185-116">它會使用以屬性標注的 .NET 類型來定義應用程式的 gRPC 服務和訊息。</span><span class="sxs-lookup"><span data-stu-id="98185-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="98185-117">建立程式碼優先 gRPC 服務的第一個步驟是定義程式碼合約：</span><span class="sxs-lookup"><span data-stu-id="98185-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="98185-118">建立將由伺服器和用戶端共用的新專案。</span><span class="sxs-lookup"><span data-stu-id="98185-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="98185-119">新增 [protobuf-網路。Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 套件參考。</span><span class="sxs-lookup"><span data-stu-id="98185-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="98185-120">建立服務和資料合約類型。</span><span class="sxs-lookup"><span data-stu-id="98185-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="98185-121">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="98185-121">The preceding code:</span></span>

* <span data-ttu-id="98185-122">定義 `HelloRequest` 和 `HelloReply` 訊息。</span><span class="sxs-lookup"><span data-stu-id="98185-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="98185-123">`IGreeterService`使用一元 gRPC 方法定義合約介面 `SayHelloAsync` 。</span><span class="sxs-lookup"><span data-stu-id="98185-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="98185-124">服務合約會在伺服器上執行，並從用戶端呼叫。</span><span class="sxs-lookup"><span data-stu-id="98185-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="98185-125">服務介面上定義的方法必須符合特定簽章，這取決於它們是一元、伺服器串流、用戶端資料流程或雙向串流。</span><span class="sxs-lookup"><span data-stu-id="98185-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="98185-126">如需定義服務合約的詳細資訊，請參閱 [protobuf-網路。Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)使用者入門檔。</span><span class="sxs-lookup"><span data-stu-id="98185-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="98185-127">建立程式碼優先 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="98185-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="98185-128">將 gRPC 程式碼優先服務新增至 ASP.NET Core 應用程式：</span><span class="sxs-lookup"><span data-stu-id="98185-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="98185-129">新增 [protobuf-網路。Grpc. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) 封裝參考。</span><span class="sxs-lookup"><span data-stu-id="98185-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="98185-130">加入共用程式碼合約專案的參考。</span><span class="sxs-lookup"><span data-stu-id="98185-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="98185-131">建立新的檔案 `GreeterService.cs` ，並執行 `IGreeterService` 服務介面：</span><span class="sxs-lookup"><span data-stu-id="98185-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="98185-132">更新檔案 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="98185-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="98185-133">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="98185-133">In the preceding code:</span></span>

* <span data-ttu-id="98185-134">`AddCodeFirstGrpc` 註冊啟用程式碼優先的服務。</span><span class="sxs-lookup"><span data-stu-id="98185-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="98185-135">`MapGrpcService<GreeterService>` 加入程式碼優先的服務端點。</span><span class="sxs-lookup"><span data-stu-id="98185-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="98185-136">使用程式碼優先的 gRPC 服務和檔案 `.proto` 可以並存存在於相同的應用程式中。</span><span class="sxs-lookup"><span data-stu-id="98185-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="98185-137">所有 gRPC 服務都會使用 [gRPC 服務](xref:grpc/configuration#configure-services-options)設定。</span><span class="sxs-lookup"><span data-stu-id="98185-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="98185-138">建立程式碼優先 gRPC 用戶端</span><span class="sxs-lookup"><span data-stu-id="98185-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="98185-139">程式碼優先 gRPC 用戶端會使用服務合約來呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="98185-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="98185-140">若要使用程式碼優先用戶端呼叫 gRPC 服務：</span><span class="sxs-lookup"><span data-stu-id="98185-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="98185-141">新增 [protobuf-網路。Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 套件參考。</span><span class="sxs-lookup"><span data-stu-id="98185-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="98185-142">加入共用程式碼合約專案的參考。</span><span class="sxs-lookup"><span data-stu-id="98185-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="98185-143">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="98185-143">The preceding code:</span></span>

* <span data-ttu-id="98185-144">建立 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="98185-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="98185-145">使用擴充方法，從通道建立程式碼優先用戶端 `CreateGrpcService<IGreeterService>` 。</span><span class="sxs-lookup"><span data-stu-id="98185-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="98185-146">使用呼叫 gRPC 服務 `SayHelloAsync` 。</span><span class="sxs-lookup"><span data-stu-id="98185-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="98185-147">程式碼優先 gRPC 用戶端是從通道建立。</span><span class="sxs-lookup"><span data-stu-id="98185-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="98185-148">就像一般用戶端一樣，程式碼優先用戶端會使用其 [通道](xref:grpc/configuration#configure-client-options)設定。</span><span class="sxs-lookup"><span data-stu-id="98185-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98185-149">其他資源</span><span class="sxs-lookup"><span data-stu-id="98185-149">Additional resources</span></span>

* [<span data-ttu-id="98185-150">protobuf-net。Grpc 網站</span><span class="sxs-lookup"><span data-stu-id="98185-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="98185-151">protobuf-net。Grpc GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="98185-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
