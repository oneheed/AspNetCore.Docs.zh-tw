---
title: ASP.NET Core gRPC 中的安全性考慮
author: jamesnk
description: 瞭解 ASP.NET Core 的 gRPC 安全性考慮。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/security
ms.openlocfilehash: a7a595a71f988377bf25c500f04da2add3d85aef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058827"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="65923-103">ASP.NET Core gRPC 中的安全性考慮</span><span class="sxs-lookup"><span data-stu-id="65923-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="65923-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="65923-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="65923-105">本文提供使用 .NET Core 保護 gRPC 的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="65923-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="65923-106">傳輸安全性</span><span class="sxs-lookup"><span data-stu-id="65923-106">Transport security</span></span>

<span data-ttu-id="65923-107">gRPC 訊息是使用 HTTP/2 來傳送和接收。</span><span class="sxs-lookup"><span data-stu-id="65923-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="65923-108">建議您：</span><span class="sxs-lookup"><span data-stu-id="65923-108">We recommend:</span></span>

* <span data-ttu-id="65923-109">[傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246) 用來保護生產 gRPC 應用程式中的訊息。</span><span class="sxs-lookup"><span data-stu-id="65923-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="65923-110">gRPC 服務應該只接聽並回應安全的埠。</span><span class="sxs-lookup"><span data-stu-id="65923-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="65923-111">TLS 是在 Kestrel 中設定。</span><span class="sxs-lookup"><span data-stu-id="65923-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="65923-112">如需有關設定 Kestrel 端點的詳細資訊，請參閱 [Kestrel 端點](xref:fundamentals/servers/kestrel#endpoint-configuration)設定。</span><span class="sxs-lookup"><span data-stu-id="65923-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="65923-113">例外狀況</span><span class="sxs-lookup"><span data-stu-id="65923-113">Exceptions</span></span>

<span data-ttu-id="65923-114">例外狀況訊息通常會視為不應該向用戶端顯示的機密資料。</span><span class="sxs-lookup"><span data-stu-id="65923-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="65923-115">根據預設，gRPC 不會將 gRPC 服務擲回之例外狀況的詳細資料傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="65923-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="65923-116">相反地，用戶端會收到一般訊息，指出發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="65923-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="65923-117">您可以使用 [EnableDetailedErrors](xref:grpc/configuration#configure-services-options)來覆寫用戶端的例外狀況訊息傳遞 (例如開發或測試) 。</span><span class="sxs-lookup"><span data-stu-id="65923-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="65923-118">例外狀況訊息不應該在生產應用程式中公開給用戶端。</span><span class="sxs-lookup"><span data-stu-id="65923-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="65923-119">訊息大小限制</span><span class="sxs-lookup"><span data-stu-id="65923-119">Message size limits</span></span>

<span data-ttu-id="65923-120">GRPC 用戶端和服務的傳入訊息會載入到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="65923-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="65923-121">訊息大小限制是一種機制，可協助防止 gRPC 耗用過度的資源。</span><span class="sxs-lookup"><span data-stu-id="65923-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="65923-122">gRPC 會使用每個訊息的大小限制來管理傳入和傳出訊息。</span><span class="sxs-lookup"><span data-stu-id="65923-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="65923-123">根據預設，gRPC 會將傳入訊息限制為 4 MB。</span><span class="sxs-lookup"><span data-stu-id="65923-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="65923-124">外寄訊息沒有限制。</span><span class="sxs-lookup"><span data-stu-id="65923-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="65923-125">在伺服器上，您可以使用下列方式，針對應用程式中的所有服務設定 gRPC 訊息限制 `AddGrpc` ：</span><span class="sxs-lookup"><span data-stu-id="65923-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="65923-126">您也可以使用來設定個別服務的限制 `AddServiceOptions<TService>` 。</span><span class="sxs-lookup"><span data-stu-id="65923-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="65923-127">如需有關設定訊息大小限制的詳細資訊，請參閱 [gRPC configuration](xref:grpc/configuration)。</span><span class="sxs-lookup"><span data-stu-id="65923-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="65923-128">用戶端憑證驗證</span><span class="sxs-lookup"><span data-stu-id="65923-128">Client certificate validation</span></span>

<span data-ttu-id="65923-129">[用戶端憑證](https://tools.ietf.org/html/rfc5246#section-7.4.4) 最初會在建立連線時進行驗證。</span><span class="sxs-lookup"><span data-stu-id="65923-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="65923-130">根據預設，Kestrel 不會執行額外的連線用戶端憑證驗證。</span><span class="sxs-lookup"><span data-stu-id="65923-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="65923-131">我們建議 gRPC 使用用戶端憑證保護的服務使用 [AspNetCore。憑證](xref:security/authentication/certauth) 套件。</span><span class="sxs-lookup"><span data-stu-id="65923-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="65923-132">ASP.NET Core 憑證驗證會對用戶端憑證執行額外的驗證，包括：</span><span class="sxs-lookup"><span data-stu-id="65923-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="65923-133">憑證具有有效的擴充金鑰使用 (EKU) </span><span class="sxs-lookup"><span data-stu-id="65923-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="65923-134">在其有效期間內</span><span class="sxs-lookup"><span data-stu-id="65923-134">Is within its validity period</span></span>
* <span data-ttu-id="65923-135">檢查憑證撤銷</span><span class="sxs-lookup"><span data-stu-id="65923-135">Check certificate revocation</span></span>
