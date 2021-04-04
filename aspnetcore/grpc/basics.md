---
title: 搭配 C# 的 gRPC 服務
author: juntaoluo
description: '瞭解使用 c # 撰寫 gRPC 服務時的基本概念。'
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 37f3b0c902d272cf8970265c8e964a1b2a7ddf00
ms.sourcegitcommit: 7e593abc85602429d94a20603936a4725838aeea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293433"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="1c19b-103">使用 C gRPC 服務\#</span><span class="sxs-lookup"><span data-stu-id="1c19b-103">gRPC services with C\#</span></span>

<span data-ttu-id="1c19b-104">本檔概述以 c # 撰寫 [gRPC](https://grpc.io/docs/guides/) 應用程式所需的概念。</span><span class="sxs-lookup"><span data-stu-id="1c19b-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="1c19b-105">本文所涵蓋的主題適用于以 [C 核心](https://grpc.io/blog/grpc-stacks)為基礎和 ASP.NET Core 為基礎的 gRPC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="1c19b-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="1c19b-106">proto 檔案</span><span class="sxs-lookup"><span data-stu-id="1c19b-106">proto file</span></span>

<span data-ttu-id="1c19b-107">gRPC 使用合約優先的方法來開發 API。</span><span class="sxs-lookup"><span data-stu-id="1c19b-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="1c19b-108">根據預設， (protobuf) 的通訊協定緩衝區作為介面定義語言 (IDL) 。</span><span class="sxs-lookup"><span data-stu-id="1c19b-108">Protocol buffers (protobuf) are used as the Interface Definition Language (IDL) by default.</span></span> <span data-ttu-id="1c19b-109">*\* Proto* 檔案包含：</span><span class="sxs-lookup"><span data-stu-id="1c19b-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="1c19b-110">GRPC 服務的定義。</span><span class="sxs-lookup"><span data-stu-id="1c19b-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="1c19b-111">用戶端與伺服器之間傳送的訊息。</span><span class="sxs-lookup"><span data-stu-id="1c19b-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="1c19b-112">如需 protobuf 檔語法的詳細資訊，請參閱 <xref:grpc/protobuf> 。</span><span class="sxs-lookup"><span data-stu-id="1c19b-112">For more information on the syntax of protobuf files, see <xref:grpc/protobuf>.</span></span>

<span data-ttu-id="1c19b-113">例如，請考慮開始 [使用 gRPC service](xref:tutorials/grpc/grpc-start)中所使用的 *歡迎* 檔：</span><span class="sxs-lookup"><span data-stu-id="1c19b-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="1c19b-114">定義 `Greeter` 服務。</span><span class="sxs-lookup"><span data-stu-id="1c19b-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="1c19b-115">`Greeter`服務會定義 `SayHello` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="1c19b-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="1c19b-116">`SayHello` 傳送 `HelloRequest` 訊息並接收 `HelloReply` 訊息：</span><span class="sxs-lookup"><span data-stu-id="1c19b-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="1c19b-117">將 proto 檔案新增至 C \# 應用程式</span><span class="sxs-lookup"><span data-stu-id="1c19b-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="1c19b-118">藉由將 *\* proto* 檔案新增至專案群組，即可將該檔案包含在專案中 `<Protobuf>` ：</span><span class="sxs-lookup"><span data-stu-id="1c19b-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="1c19b-119">根據預設， `<Protobuf>` 參考會產生實體用戶端和服務基類。</span><span class="sxs-lookup"><span data-stu-id="1c19b-119">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="1c19b-120">參考專案的 `GrpcServices` 屬性可以用來限制產生 c # 資產。</span><span class="sxs-lookup"><span data-stu-id="1c19b-120">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="1c19b-121">有效 `GrpcServices` 的選項包括：</span><span class="sxs-lookup"><span data-stu-id="1c19b-121">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="1c19b-122">`Both` 不存在時 (預設值) </span><span class="sxs-lookup"><span data-stu-id="1c19b-122">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="1c19b-123">適用于 proto 檔案的 c # 工具支援</span><span class="sxs-lookup"><span data-stu-id="1c19b-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="1c19b-124">需要工具套件 [Grpc](https://www.nuget.org/packages/Grpc.Tools/) ，才能從 *\* Proto* 檔案產生 c # 資產。</span><span class="sxs-lookup"><span data-stu-id="1c19b-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="1c19b-125">產生的資產 (檔) ：</span><span class="sxs-lookup"><span data-stu-id="1c19b-125">The generated assets (files):</span></span>

* <span data-ttu-id="1c19b-126">每次建立專案時，會根據需要來產生。</span><span class="sxs-lookup"><span data-stu-id="1c19b-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="1c19b-127">不會加入至專案或簽入原始檔控制。</span><span class="sxs-lookup"><span data-stu-id="1c19b-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="1c19b-128">是包含在 *obj* 目錄中的組建成品。</span><span class="sxs-lookup"><span data-stu-id="1c19b-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="1c19b-129">伺服器和用戶端專案都需要此封裝。</span><span class="sxs-lookup"><span data-stu-id="1c19b-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="1c19b-130">`Grpc.AspNetCore`中繼套件包含的參考 `Grpc.Tools` 。</span><span class="sxs-lookup"><span data-stu-id="1c19b-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="1c19b-131">您可以 `Grpc.AspNetCore` 使用 Visual Studio 中的封裝管理員，或將加入至專案檔，以新增伺服器專案 `<PackageReference>` ：</span><span class="sxs-lookup"><span data-stu-id="1c19b-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="1c19b-132">用戶端專案應該直接參考 `Grpc.Tools` 使用 gRPC 用戶端所需的其他套件。</span><span class="sxs-lookup"><span data-stu-id="1c19b-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="1c19b-133">在執行時間不需要工具套件，因此相依性會標示為 `PrivateAssets="All"` ：</span><span class="sxs-lookup"><span data-stu-id="1c19b-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-14)]

## <a name="generated-c-assets"></a><span data-ttu-id="1c19b-134">產生的 c # 資產</span><span class="sxs-lookup"><span data-stu-id="1c19b-134">Generated C# assets</span></span>

<span data-ttu-id="1c19b-135">工具套件會產生 c # 類型，代表包含的 *\* proto* 檔案中定義的訊息。</span><span class="sxs-lookup"><span data-stu-id="1c19b-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="1c19b-136">針對伺服器端資產，會產生抽象服務基底類型。</span><span class="sxs-lookup"><span data-stu-id="1c19b-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="1c19b-137">基底類型包含 gRPC 中包含的所有呼叫的定義 *。*</span><span class="sxs-lookup"><span data-stu-id="1c19b-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="1c19b-138">建立衍生自此基底類型的具體服務執行，並執行 gRPC 呼叫的邏輯。</span><span class="sxs-lookup"><span data-stu-id="1c19b-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="1c19b-139">如 `greet.proto` 先前所述的範例， `GreeterBase` 會產生包含虛擬方法的抽象型別 `SayHello` 。</span><span class="sxs-lookup"><span data-stu-id="1c19b-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="1c19b-140">具體的實作為會 `GreeterService` 覆寫方法，並執行處理 gRPC 呼叫的邏輯。</span><span class="sxs-lookup"><span data-stu-id="1c19b-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="1c19b-141">若為用戶端資產，則會產生具體的用戶端類型。</span><span class="sxs-lookup"><span data-stu-id="1c19b-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="1c19b-142">在 *proto* 檔中的 gRPC 呼叫會轉譯成具象型別上的方法，可以呼叫。</span><span class="sxs-lookup"><span data-stu-id="1c19b-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="1c19b-143">針對 `greet.proto` ，先前所述的範例 `GreeterClient` 會產生實體型別。</span><span class="sxs-lookup"><span data-stu-id="1c19b-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="1c19b-144">呼叫 `GreeterClient.SayHelloAsync` 以初始化對伺服器的 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="1c19b-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="1c19b-145">依預設，會針對專案群組中包含的每個 *\* proto* 檔案產生伺服器和用戶端資產 `<Protobuf>` 。</span><span class="sxs-lookup"><span data-stu-id="1c19b-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="1c19b-146">為確保伺服器專案中只會產生伺服器資產， `GrpcServices` 屬性會設定為 `Server` 。</span><span class="sxs-lookup"><span data-stu-id="1c19b-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="1c19b-147">同樣地，在 `Client` 用戶端專案中，屬性會設定為。</span><span class="sxs-lookup"><span data-stu-id="1c19b-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c19b-148">其他資源</span><span class="sxs-lookup"><span data-stu-id="1c19b-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
