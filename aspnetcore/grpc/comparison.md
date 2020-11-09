---
title: 比較 gRPC 服務與 HTTP API
author: jamesnk
description: 瞭解 gRPC 如何與 HTTP Api 和建議案例的比較。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
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
uid: grpc/comparison
ms.openlocfilehash: 0fb50f07153f5f9953b667fe32062ad24b2bd66d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059945"
---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="fe44d-103">比較 gRPC 服務與 HTTP API</span><span class="sxs-lookup"><span data-stu-id="fe44d-103">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="fe44d-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fe44d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fe44d-105">本文說明如何使用 JSON (（包括 ASP.NET Core [Web api](xref:web-api/index)) ）來比較[GRPC Services](https://grpc.io/docs/guides/)與 HTTP api。</span><span class="sxs-lookup"><span data-stu-id="fe44d-105">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="fe44d-106">用來為您的應用程式提供 API 的技術是很重要的選擇，而 gRPC 提供與 HTTP Api 相較之下的獨特優點。</span><span class="sxs-lookup"><span data-stu-id="fe44d-106">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="fe44d-107">本文討論 gRPC 的優點和缺點，並建議在其他技術上使用 gRPC 的案例。</span><span class="sxs-lookup"><span data-stu-id="fe44d-107">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="fe44d-108">高層級比較</span><span class="sxs-lookup"><span data-stu-id="fe44d-108">High-level comparison</span></span>

<span data-ttu-id="fe44d-109">下表提供 gRPC 和 HTTP Api 與 JSON 之間功能的高階比較。</span><span class="sxs-lookup"><span data-stu-id="fe44d-109">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="fe44d-110">功能</span><span class="sxs-lookup"><span data-stu-id="fe44d-110">Feature</span></span>          | <span data-ttu-id="fe44d-111">gRPC</span><span class="sxs-lookup"><span data-stu-id="fe44d-111">gRPC</span></span>                                               | <span data-ttu-id="fe44d-112">HTTP Api 與 JSON</span><span class="sxs-lookup"><span data-stu-id="fe44d-112">HTTP APIs with JSON</span></span>           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| <span data-ttu-id="fe44d-113">合約</span><span class="sxs-lookup"><span data-stu-id="fe44d-113">Contract</span></span>         | <span data-ttu-id="fe44d-114">必要 ( *。 proto* ) </span><span class="sxs-lookup"><span data-stu-id="fe44d-114">Required ( *.proto* )</span></span>                                | <span data-ttu-id="fe44d-115">選擇性的 (OpenAPI) </span><span class="sxs-lookup"><span data-stu-id="fe44d-115">Optional (OpenAPI)</span></span>            |
| <span data-ttu-id="fe44d-116">通訊協定</span><span class="sxs-lookup"><span data-stu-id="fe44d-116">Protocol</span></span>         | <span data-ttu-id="fe44d-117">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fe44d-117">HTTP/2</span></span>                                             | <span data-ttu-id="fe44d-118">HTTP</span><span class="sxs-lookup"><span data-stu-id="fe44d-118">HTTP</span></span>                          |
| <span data-ttu-id="fe44d-119">Payload</span><span class="sxs-lookup"><span data-stu-id="fe44d-119">Payload</span></span>          | [<span data-ttu-id="fe44d-120">Protobuf (small、binary) </span><span class="sxs-lookup"><span data-stu-id="fe44d-120">Protobuf (small, binary)</span></span>](#performance)           | <span data-ttu-id="fe44d-121">JSON (大型、人類看得懂的) </span><span class="sxs-lookup"><span data-stu-id="fe44d-121">JSON (large, human readable)</span></span>  |
| <span data-ttu-id="fe44d-122">Prescriptiveness</span><span class="sxs-lookup"><span data-stu-id="fe44d-122">Prescriptiveness</span></span> | [<span data-ttu-id="fe44d-123">嚴格規格</span><span class="sxs-lookup"><span data-stu-id="fe44d-123">Strict specification</span></span>](#strict-specification)      | <span data-ttu-id="fe44d-124">鬆散。</span><span class="sxs-lookup"><span data-stu-id="fe44d-124">Loose.</span></span> <span data-ttu-id="fe44d-125">任何 HTTP 都有效。</span><span class="sxs-lookup"><span data-stu-id="fe44d-125">Any HTTP is valid.</span></span>     |
| <span data-ttu-id="fe44d-126">串流</span><span class="sxs-lookup"><span data-stu-id="fe44d-126">Streaming</span></span>        | [<span data-ttu-id="fe44d-127">用戶端、伺服器、雙向</span><span class="sxs-lookup"><span data-stu-id="fe44d-127">Client, server, bi-directional</span></span>](#streaming)       | <span data-ttu-id="fe44d-128">用戶端、伺服器</span><span class="sxs-lookup"><span data-stu-id="fe44d-128">Client, server</span></span>                |
| <span data-ttu-id="fe44d-129">瀏覽器支援</span><span class="sxs-lookup"><span data-stu-id="fe44d-129">Browser support</span></span>  | [<span data-ttu-id="fe44d-130">無 (需要 grpc-web) </span><span class="sxs-lookup"><span data-stu-id="fe44d-130">No (requires grpc-web)</span></span>](#limited-browser-support) | <span data-ttu-id="fe44d-131">是</span><span class="sxs-lookup"><span data-stu-id="fe44d-131">Yes</span></span>                           |
| <span data-ttu-id="fe44d-132">安全性</span><span class="sxs-lookup"><span data-stu-id="fe44d-132">Security</span></span>         | <span data-ttu-id="fe44d-133">傳輸 (TLS) </span><span class="sxs-lookup"><span data-stu-id="fe44d-133">Transport (TLS)</span></span>                                    | <span data-ttu-id="fe44d-134">傳輸 (TLS) </span><span class="sxs-lookup"><span data-stu-id="fe44d-134">Transport (TLS)</span></span>               |
| <span data-ttu-id="fe44d-135">用戶端程式代碼產生</span><span class="sxs-lookup"><span data-stu-id="fe44d-135">Client code-generation</span></span> | [<span data-ttu-id="fe44d-136">是</span><span class="sxs-lookup"><span data-stu-id="fe44d-136">Yes</span></span>](#code-generation)                      | <span data-ttu-id="fe44d-137">OpenAPI + 協力廠商工具</span><span class="sxs-lookup"><span data-stu-id="fe44d-137">OpenAPI + third-party tooling</span></span> |

## <a name="grpc-strengths"></a><span data-ttu-id="fe44d-138">gRPC 的強項</span><span class="sxs-lookup"><span data-stu-id="fe44d-138">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="fe44d-139">效能</span><span class="sxs-lookup"><span data-stu-id="fe44d-139">Performance</span></span>

<span data-ttu-id="fe44d-140">gRPC 訊息是使用 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)（有效率的二進位訊息格式）進行序列化。</span><span class="sxs-lookup"><span data-stu-id="fe44d-140">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="fe44d-141">Protobuf 會在伺服器和用戶端上迅速序列化。</span><span class="sxs-lookup"><span data-stu-id="fe44d-141">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="fe44d-142">Protobuf 序列化會產生小型訊息承載，在有限的頻寬案例（例如行動應用程式）中很重要。</span><span class="sxs-lookup"><span data-stu-id="fe44d-142">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="fe44d-143">gRPC 是針對 HTTP/2 設計的，這是 HTTP 的主要版本，可透過 HTTP 1.x 提供顯著的效能優勢：</span><span class="sxs-lookup"><span data-stu-id="fe44d-143">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="fe44d-144">二進位框架和壓縮。</span><span class="sxs-lookup"><span data-stu-id="fe44d-144">Binary framing and compression.</span></span> <span data-ttu-id="fe44d-145">HTTP/2 通訊協定在傳送和接收時都是精簡且有效率的。</span><span class="sxs-lookup"><span data-stu-id="fe44d-145">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="fe44d-146">透過單一 TCP 連接的多個 HTTP/2 呼叫多工。</span><span class="sxs-lookup"><span data-stu-id="fe44d-146">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="fe44d-147">多工化可消除 [行標頭封鎖](https://en.wikipedia.org/wiki/Head-of-line_blocking)。</span><span class="sxs-lookup"><span data-stu-id="fe44d-147">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="fe44d-148">HTTP/2 不是 gRPC 專屬的。</span><span class="sxs-lookup"><span data-stu-id="fe44d-148">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="fe44d-149">許多要求類型（包括具有 JSON 的 HTTP Api）可以使用 HTTP/2，並受益于其效能改進。</span><span class="sxs-lookup"><span data-stu-id="fe44d-149">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="fe44d-150">程式碼產生</span><span class="sxs-lookup"><span data-stu-id="fe44d-150">Code generation</span></span>

<span data-ttu-id="fe44d-151">所有 gRPC 架構都提供最先進的程式碼產生支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-151">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="fe44d-152">GRPC 開發的核心檔案是定義 gRPC 服務和訊息合約的[proto 檔。](https://developers.google.com/protocol-buffers/docs/proto3)</span><span class="sxs-lookup"><span data-stu-id="fe44d-152">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="fe44d-153">從這個檔案 gRPC 架構會產生服務基類、訊息和完整用戶端的程式碼。</span><span class="sxs-lookup"><span data-stu-id="fe44d-153">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="fe44d-154">藉由共用伺服器和用戶端之間的 *proto* 檔案，就可以從端對端產生訊息和用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="fe44d-154">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="fe44d-155">用戶端的程式碼產生會在用戶端和伺服器上消除重複的訊息，並為您建立強型別的用戶端。</span><span class="sxs-lookup"><span data-stu-id="fe44d-155">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="fe44d-156">不需要撰寫用戶端就能將大量開發時間節省在具有許多服務的應用程式中。</span><span class="sxs-lookup"><span data-stu-id="fe44d-156">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="fe44d-157">嚴格規格</span><span class="sxs-lookup"><span data-stu-id="fe44d-157">Strict specification</span></span>

<span data-ttu-id="fe44d-158">具有 JSON 之 HTTP API 的正式規格不存在。</span><span class="sxs-lookup"><span data-stu-id="fe44d-158">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="fe44d-159">開發人員會爭論 Url、HTTP 動詞命令和回應碼的最佳格式。</span><span class="sxs-lookup"><span data-stu-id="fe44d-159">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="fe44d-160">[GRPC 規格](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)是 gRPC 服務必須遵循的格式規範。</span><span class="sxs-lookup"><span data-stu-id="fe44d-160">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="fe44d-161">gRPC 可消除爭論並節省開發人員的時間，因為 gRPC 在各平臺和執行之間都是一致的。</span><span class="sxs-lookup"><span data-stu-id="fe44d-161">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="fe44d-162">串流</span><span class="sxs-lookup"><span data-stu-id="fe44d-162">Streaming</span></span>

<span data-ttu-id="fe44d-163">HTTP/2 提供長期即時通訊資料流程的基礎。</span><span class="sxs-lookup"><span data-stu-id="fe44d-163">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="fe44d-164">gRPC 透過 HTTP/2 提供對串流的頂級支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-164">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="fe44d-165">GRPC 服務支援所有串流組合：</span><span class="sxs-lookup"><span data-stu-id="fe44d-165">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="fe44d-166">一元 (沒有串流) </span><span class="sxs-lookup"><span data-stu-id="fe44d-166">Unary (no streaming)</span></span>
* <span data-ttu-id="fe44d-167">伺服器對用戶端串流</span><span class="sxs-lookup"><span data-stu-id="fe44d-167">Server to client streaming</span></span>
* <span data-ttu-id="fe44d-168">用戶端對伺服器串流</span><span class="sxs-lookup"><span data-stu-id="fe44d-168">Client to server streaming</span></span>
* <span data-ttu-id="fe44d-169">雙向串流</span><span class="sxs-lookup"><span data-stu-id="fe44d-169">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="fe44d-170">期限/超時和取消</span><span class="sxs-lookup"><span data-stu-id="fe44d-170">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="fe44d-171">gRPC 可讓用戶端指定他們願意等待 RPC 完成的時間長度。</span><span class="sxs-lookup"><span data-stu-id="fe44d-171">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="fe44d-172">[期限](https://grpc.io/blog/deadlines)會傳送至伺服器，而且伺服器可以決定當超過期限時要採取的動作。</span><span class="sxs-lookup"><span data-stu-id="fe44d-172">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="fe44d-173">例如，伺服器可能會在超時時取消進行中的 gRPC/HTTP/資料庫要求。</span><span class="sxs-lookup"><span data-stu-id="fe44d-173">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="fe44d-174">透過子 gRPC 呼叫傳播期限和取消，有助於強制執行資源使用量限制。</span><span class="sxs-lookup"><span data-stu-id="fe44d-174">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="fe44d-175">gRPC 建議的案例</span><span class="sxs-lookup"><span data-stu-id="fe44d-175">gRPC recommended scenarios</span></span>

<span data-ttu-id="fe44d-176">gRPC 適用于下列案例：</span><span class="sxs-lookup"><span data-stu-id="fe44d-176">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="fe44d-177">**微服務** ： gRPC 是針對低延遲和高輸送量的通訊所設計。</span><span class="sxs-lookup"><span data-stu-id="fe44d-177">**Microservices** : gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="fe44d-178">gRPC 非常適合於高效率的輕量微服務。</span><span class="sxs-lookup"><span data-stu-id="fe44d-178">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="fe44d-179">**點對點即時通訊** ： gRPC 有絕佳的雙向串流支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-179">**Point-to-point real-time communication** : gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="fe44d-180">gRPC services 可以即時推播訊息，而不會進行輪詢。</span><span class="sxs-lookup"><span data-stu-id="fe44d-180">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="fe44d-181">**多語言環境** ： gRPC 工具支援所有熱門的開發語言，讓 gRPC 成為多國語言環境的理想選擇。</span><span class="sxs-lookup"><span data-stu-id="fe44d-181">**Polyglot environments** : gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="fe44d-182">**網路受限的環境** ： gRPC 訊息會以 Protobuf （輕量訊息格式）進行序列化。</span><span class="sxs-lookup"><span data-stu-id="fe44d-182">**Network constrained environments** : gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="fe44d-183">GRPC 訊息一律小於相等的 JSON 訊息。</span><span class="sxs-lookup"><span data-stu-id="fe44d-183">A gRPC message is always smaller than an equivalent JSON message.</span></span>
* <span data-ttu-id="fe44d-184">**處理序間通訊 (ipc)** ：如 Unix 網域通訊端和具名管道等 ipc 傳輸，可與 gRPC 搭配使用，以在同一部電腦上的應用程式之間進行通訊。</span><span class="sxs-lookup"><span data-stu-id="fe44d-184">**Inter-process communication (IPC)** : IPC transports such as Unix domain sockets and named pipes can be used with gRPC to communicate between apps on the same machine.</span></span> <span data-ttu-id="fe44d-185">如需詳細資訊，請參閱<xref:grpc/interprocess>。</span><span class="sxs-lookup"><span data-stu-id="fe44d-185">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="fe44d-186">gRPC 弱點</span><span class="sxs-lookup"><span data-stu-id="fe44d-186">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="fe44d-187">有限的瀏覽器支援</span><span class="sxs-lookup"><span data-stu-id="fe44d-187">Limited browser support</span></span>

<span data-ttu-id="fe44d-188">目前無法直接從瀏覽器呼叫 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="fe44d-188">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="fe44d-189">gRPC 會大量使用 HTTP/2 功能，而且沒有任何瀏覽器會提供 web 要求所需的控制層級來支援 gRPC 用戶端。</span><span class="sxs-lookup"><span data-stu-id="fe44d-189">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="fe44d-190">例如，瀏覽器不允許呼叫者要求使用 HTTP/2，或提供基礎 HTTP/2 框架的存取權。</span><span class="sxs-lookup"><span data-stu-id="fe44d-190">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="fe44d-191">有兩種常見的方法可將 gRPC 帶入瀏覽器應用程式：</span><span class="sxs-lookup"><span data-stu-id="fe44d-191">There are two common approaches to bring gRPC to browser apps:</span></span>

* <span data-ttu-id="fe44d-192">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) 是 gRPC 小組提供的額外技術，可在瀏覽器中提供 gRPC 支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-192">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides gRPC support in the browser.</span></span> <span data-ttu-id="fe44d-193">gRPC Web 可讓瀏覽器應用程式受益于 gRPC 的高效能和低網路使用。</span><span class="sxs-lookup"><span data-stu-id="fe44d-193">gRPC-Web allows browser apps to benefit from the high-performance and low network usage of gRPC.</span></span> <span data-ttu-id="fe44d-194">並非所有 gRPC 的功能都受 gRPC Web 支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-194">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="fe44d-195">用戶端和雙向串流不受支援，而且伺服器串流的支援有限。</span><span class="sxs-lookup"><span data-stu-id="fe44d-195">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

  <span data-ttu-id="fe44d-196">.NET Core 支援 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="fe44d-196">.NET Core has support for gRPC-Web.</span></span> <span data-ttu-id="fe44d-197">如需詳細資訊，請參閱<xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="fe44d-197">For more information, see <xref:grpc/browser>.</span></span>

* <span data-ttu-id="fe44d-198">RESTful JSON Web Api 可以從 gRPC 服務自動建立，方法是使用 [HTTP 中繼資料](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)來標注該 *proto* 檔。</span><span class="sxs-lookup"><span data-stu-id="fe44d-198">RESTful JSON Web APIs can be automatically created from gRPC services by annotating the *.proto* file with [HTTP metadata](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).</span></span> <span data-ttu-id="fe44d-199">這可讓應用程式同時支援 gRPC 和 JSON web Api，而不需要為兩者重複建立個別服務的工作。</span><span class="sxs-lookup"><span data-stu-id="fe44d-199">This allows an app to support both gRPC and JSON web APIs, without duplicating effort of building separate services for both.</span></span>

  <span data-ttu-id="fe44d-200">.NET Core 具有從 gRPC 服務建立 JSON web Api 的實驗性支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-200">.NET Core has experimental support for creating JSON web APIs from gRPC services.</span></span> <span data-ttu-id="fe44d-201">如需詳細資訊，請參閱<xref:grpc/httpapi>。</span><span class="sxs-lookup"><span data-stu-id="fe44d-201">For more information, see <xref:grpc/httpapi>.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="fe44d-202">不是人類看得懂的</span><span class="sxs-lookup"><span data-stu-id="fe44d-202">Not human readable</span></span>

<span data-ttu-id="fe44d-203">HTTP API 要求會以文字的形式傳送，並且可由人類讀取及建立。</span><span class="sxs-lookup"><span data-stu-id="fe44d-203">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="fe44d-204">gRPC 訊息預設會以 Protobuf 編碼。</span><span class="sxs-lookup"><span data-stu-id="fe44d-204">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="fe44d-205">雖然 Protobuf 的傳送和接收效率很高，但其二進位格式不是人類看得懂的。</span><span class="sxs-lookup"><span data-stu-id="fe44d-205">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="fe44d-206">Protobuf 需要在 *proto* 檔案中指定訊息的介面描述，才能正確還原序列化。</span><span class="sxs-lookup"><span data-stu-id="fe44d-206">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="fe44d-207">需要額外的工具才能分析網路上的 Protobuf 承載，並以手動方式撰寫要求。</span><span class="sxs-lookup"><span data-stu-id="fe44d-207">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="fe44d-208">[伺服器反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)和[gRPC 命令列工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)等功能，可協助您使用二進位 Protobuf 訊息。</span><span class="sxs-lookup"><span data-stu-id="fe44d-208">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="fe44d-209">此外，Protobuf 訊息支援在 [JSON 之間進行轉換](https://developers.google.com/protocol-buffers/docs/proto3#json)。</span><span class="sxs-lookup"><span data-stu-id="fe44d-209">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="fe44d-210">內建的 JSON 轉換可讓您有效率地在進行偵錯工具時，將 Protobuf 訊息轉換成人類可讀取的表單。</span><span class="sxs-lookup"><span data-stu-id="fe44d-210">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="fe44d-211">替代架構案例</span><span class="sxs-lookup"><span data-stu-id="fe44d-211">Alternative framework scenarios</span></span>

<span data-ttu-id="fe44d-212">在下列案例中，建議使用其他架構，而不是 gRPC：</span><span class="sxs-lookup"><span data-stu-id="fe44d-212">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="fe44d-213">**瀏覽器可存取的 api** ： gRPC 在瀏覽器中未受到完整支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-213">**Browser accessible APIs** : gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="fe44d-214">gRPC-Web 可以提供瀏覽器支援，但它有一些限制，並引進伺服器 proxy。</span><span class="sxs-lookup"><span data-stu-id="fe44d-214">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="fe44d-215">**廣播即時通訊** ： gRPC 透過串流支援即時通訊，但廣播訊息到已註冊連接的概念不存在。</span><span class="sxs-lookup"><span data-stu-id="fe44d-215">**Broadcast real-time communication** : gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="fe44d-216">例如，在應該將新的聊天訊息傳送到聊天室中所有用戶端的聊天室案例中，每個 gRPC 呼叫都需要個別將新的聊天訊息串流至用戶端。</span><span class="sxs-lookup"><span data-stu-id="fe44d-216">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="fe44d-217">[SignalR](xref:signalr/introduction) 是適用于此案例的實用架構。</span><span class="sxs-lookup"><span data-stu-id="fe44d-217">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> <span data-ttu-id="fe44d-218">SignalR 具有持續性連接的概念，以及廣播訊息的內建支援。</span><span class="sxs-lookup"><span data-stu-id="fe44d-218">SignalR has the concept of persistent connections and built-in support for broadcasting messages.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe44d-219">其他資源</span><span class="sxs-lookup"><span data-stu-id="fe44d-219">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
