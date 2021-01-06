---
title: 為何將 WCF 遷移至 ASP.NET Core gRPC
author: markrendle
description: 本文提供 ASP.NET Core gRPC 適用于 Windows Communication Foundation (WCF) 開發人員想要遷移到新式架構和平臺的相關摘要。
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058684"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="78ed4-103">Windows Communication Foundation (WCF) 開發人員的 gRPC</span><span class="sxs-lookup"><span data-stu-id="78ed4-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="78ed4-104">本文提供 ASP.NET Core gRPC 適用于 Windows Communication Foundation (WCF) 開發人員想要遷移到新式架構和平臺的相關摘要。</span><span class="sxs-lookup"><span data-stu-id="78ed4-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="78ed4-105">與 WCF 的比較</span><span class="sxs-lookup"><span data-stu-id="78ed4-105">Comparison to WCF</span></span>

<span data-ttu-id="78ed4-106">雖然 gRPC 的實和方法不同，但使用 gRPC 開發和取用服務的經驗對於 WCF 開發人員來說應該是直覺的。</span><span class="sxs-lookup"><span data-stu-id="78ed4-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="78ed4-107">WCF 和 gRPC 是 RPC (遠端程序呼叫，) 具有相同目標的架構：</span><span class="sxs-lookup"><span data-stu-id="78ed4-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="78ed4-108">讓它可以像用戶端和伺服器位於相同的平臺一樣撰寫程式碼。</span><span class="sxs-lookup"><span data-stu-id="78ed4-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="78ed4-109">提供簡化的便攜網路 API。</span><span class="sxs-lookup"><span data-stu-id="78ed4-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="78ed4-110">這兩個平臺都會共用宣告和執行介面的需求，雖然宣告介面的程式不同。</span><span class="sxs-lookup"><span data-stu-id="78ed4-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="78ed4-111">GRPC 的許多 RPC 呼叫類型都支援對應到 WCF 服務可用的系結。</span><span class="sxs-lookup"><span data-stu-id="78ed4-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="78ed4-112">如需詳細資訊和範例，請參閱 [將 WCF 方案遷移至 gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)。</span><span class="sxs-lookup"><span data-stu-id="78ed4-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="78ed4-113">GRPC 的優點</span><span class="sxs-lookup"><span data-stu-id="78ed4-113">Benefits of gRPC</span></span>

<span data-ttu-id="78ed4-114">gRPC 提供比其他方法更好的架構，原因如下。</span><span class="sxs-lookup"><span data-stu-id="78ed4-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="78ed4-115">效能</span><span class="sxs-lookup"><span data-stu-id="78ed4-115">Performance</span></span>

<span data-ttu-id="78ed4-116">gRPC 使用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="78ed4-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="78ed4-117">與 HTTP/1.1 相較之下，HTTP/2：</span><span class="sxs-lookup"><span data-stu-id="78ed4-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="78ed4-118">是較小、更快速的二進位通訊協定。</span><span class="sxs-lookup"><span data-stu-id="78ed4-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="78ed4-119">更有效率地剖析電腦。</span><span class="sxs-lookup"><span data-stu-id="78ed4-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="78ed4-120">支援透過單一連接的多工要求。</span><span class="sxs-lookup"><span data-stu-id="78ed4-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="78ed4-121">多工化可讓您透過一個連線傳送多個要求，而不需要封鎖彼此的要求。</span><span class="sxs-lookup"><span data-stu-id="78ed4-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="78ed4-122">在 HTTP/1.1 中，封鎖稱為「行標頭 (住) 封鎖」。</span><span class="sxs-lookup"><span data-stu-id="78ed4-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="78ed4-123">gRPC 使用 Protobuf （有效率的二進位格式）來序列化訊息。</span><span class="sxs-lookup"><span data-stu-id="78ed4-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="78ed4-124">Protobuf 訊息為：</span><span class="sxs-lookup"><span data-stu-id="78ed4-124">Protobuf messages are:</span></span>
* <span data-ttu-id="78ed4-125">快速序列化和還原序列化。</span><span class="sxs-lookup"><span data-stu-id="78ed4-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="78ed4-126">使用較少的頻寬，而不是以文字為基礎的格式。</span><span class="sxs-lookup"><span data-stu-id="78ed4-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="78ed4-127">gRPC 是適用于具有頻寬限制之行動裝置和網路的絕佳解決方案。</span><span class="sxs-lookup"><span data-stu-id="78ed4-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="78ed4-128">互通性</span><span class="sxs-lookup"><span data-stu-id="78ed4-128">Interoperability</span></span>

<span data-ttu-id="78ed4-129">所有主要的程式設計語言和平臺都有 gRPC 的工具和程式庫，包括 .NET、JAVA、Python、Go、c + +、Node.js、Swift、Dart、Ruby 和 PHP。</span><span class="sxs-lookup"><span data-stu-id="78ed4-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="78ed4-130">由於 Protobuf 二進位電傳格式和每個平臺的有效率程式碼產生，開發人員可以建立跨平臺、高效能的應用程式。</span><span class="sxs-lookup"><span data-stu-id="78ed4-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="78ed4-131">可用性和產能</span><span class="sxs-lookup"><span data-stu-id="78ed4-131">Usability and productivity</span></span>

<span data-ttu-id="78ed4-132">gRPC 是全方位的 RPC 解決方案。</span><span class="sxs-lookup"><span data-stu-id="78ed4-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="78ed4-133">它在多種語言和平臺上都能一致地運作。</span><span class="sxs-lookup"><span data-stu-id="78ed4-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="78ed4-134">它也提供絕佳的工具，並自動產生大部分的重複使用程式碼。</span><span class="sxs-lookup"><span data-stu-id="78ed4-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="78ed4-135">如同 WCF，gRPC 會自動產生訊息和強型別的用戶端。</span><span class="sxs-lookup"><span data-stu-id="78ed4-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="78ed4-136">開發人員時間會釋出以專注于商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="78ed4-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="78ed4-137">串流</span><span class="sxs-lookup"><span data-stu-id="78ed4-137">Streaming</span></span>

<span data-ttu-id="78ed4-138">gRPC 具有完整的雙向串流，可提供 WCF 完整雙工服務的類似功能。</span><span class="sxs-lookup"><span data-stu-id="78ed4-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="78ed4-139">gRPC 串流可以透過一般的網際網路連線、負載平衡器和服務網格來運作。</span><span class="sxs-lookup"><span data-stu-id="78ed4-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="78ed4-140">期限、超時和取消</span><span class="sxs-lookup"><span data-stu-id="78ed4-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="78ed4-141">gRPC 可讓用戶端指定 RPC 完成的最長時間。</span><span class="sxs-lookup"><span data-stu-id="78ed4-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="78ed4-142">如果超過指定的期限，伺服器可以在用戶端之外取消作業。</span><span class="sxs-lookup"><span data-stu-id="78ed4-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="78ed4-143">期限和取消可透過後續的 gRPC 呼叫傳播，以協助強制執行資源使用量限制。</span><span class="sxs-lookup"><span data-stu-id="78ed4-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="78ed4-144">用戶端可以在超過期限時或稍早（如有需要）停止作業。</span><span class="sxs-lookup"><span data-stu-id="78ed4-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="78ed4-145">例如，用戶端可能會因為使用者互動而停止作業。</span><span class="sxs-lookup"><span data-stu-id="78ed4-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="78ed4-146">安全性</span><span class="sxs-lookup"><span data-stu-id="78ed4-146">Security</span></span>

<span data-ttu-id="78ed4-147">gRPC 可以使用 TLS 和 HTTP/2，在用戶端與伺服器之間提供端對端加密的連接。</span><span class="sxs-lookup"><span data-stu-id="78ed4-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="78ed4-148">用戶端憑證驗證支援可進一步提高用戶端與伺服器之間的安全性與信任。</span><span class="sxs-lookup"><span data-stu-id="78ed4-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="78ed4-149">gRPC 做為 WCF 到 .NET Core 和 .NET 5 的遷移路徑</span><span class="sxs-lookup"><span data-stu-id="78ed4-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="78ed4-150">.NET Core 和 .NET 5 標示了 Microsoft 為想要跨各種平臺提供服務的開發人員提供遠端通訊解決方案的方式。</span><span class="sxs-lookup"><span data-stu-id="78ed4-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="78ed4-151">.NET Core 和 .NET 5 支援 [呼叫 wcf 服務](/dotnet/core/additional-tools/wcf-web-service-reference-guide)，但不提供裝載 WCF 的伺服器端支援。</span><span class="sxs-lookup"><span data-stu-id="78ed4-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="78ed4-152">現代化 WCF 應用程式有兩個建議的路徑：</span><span class="sxs-lookup"><span data-stu-id="78ed4-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="78ed4-153">gRPC 是以新式技術為基礎，並已成為適用于 RPC 應用程式之開發人員群體最受歡迎的選擇。</span><span class="sxs-lookup"><span data-stu-id="78ed4-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="78ed4-154">從 .NET Core 3.0 開始，新式 .NET 平臺對 gRPC 有絕佳的支援。</span><span class="sxs-lookup"><span data-stu-id="78ed4-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="78ed4-155">將 WCF 服務遷移至使用 gRPC 有助於提供 RPC 功能、效能、新式應用程式所需的互通性。</span><span class="sxs-lookup"><span data-stu-id="78ed4-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="78ed4-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) 是一項支援將 WCF 服務裝載到 .net Core 和 .net 5 的支援。</span><span class="sxs-lookup"><span data-stu-id="78ed4-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="78ed4-157">預覽版本可供使用，且專案正在生產環境就緒。</span><span class="sxs-lookup"><span data-stu-id="78ed4-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="78ed4-158">CoreWCF 只支援 WCF 功能的子集，而遷移使用它的 .NET Framework 應用程式將需要變更程式碼和測試才能成功。</span><span class="sxs-lookup"><span data-stu-id="78ed4-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="78ed4-159">如果應用程式必須維持與呼叫 WCF 服務之現有用戶端的相容性，則 CoreWCF 是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="78ed4-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="78ed4-160">開始使用</span><span class="sxs-lookup"><span data-stu-id="78ed4-160">Get started</span></span>

<span data-ttu-id="78ed4-161">如需有關在 WCF 開發人員 ASP.NET Core 中建立 gRPC 服務的詳細指引，請參閱 [ASP.NET Core 適用于 Wcf 開發人員的 gRPC](/dotnet/architecture/grpc-for-wcf-developers)</span><span class="sxs-lookup"><span data-stu-id="78ed4-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
