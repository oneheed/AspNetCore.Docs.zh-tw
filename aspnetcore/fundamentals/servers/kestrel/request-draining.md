---
title: ASP.NET Core Kestrel web 伺服器的要求清空
author: rick-anderson
description: 瞭解如何使用 Kestrel （適用于 ASP.NET Core 的跨平臺 web 伺服器）來清空要求。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253929"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="a6646-103">ASP.NET Core Kestrel web 伺服器的要求清空</span><span class="sxs-lookup"><span data-stu-id="a6646-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="a6646-104">開啟 HTTP 連接相當耗時。</span><span class="sxs-lookup"><span data-stu-id="a6646-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="a6646-105">若是 HTTPS，也需要大量資源。</span><span class="sxs-lookup"><span data-stu-id="a6646-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="a6646-106">因此，Kestrel 會嘗試針對每個 HTTP/1.1 通訊協定重複使用連接。</span><span class="sxs-lookup"><span data-stu-id="a6646-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="a6646-107">要求主體必須完全取用，才能重複使用連線。</span><span class="sxs-lookup"><span data-stu-id="a6646-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="a6646-108">應用程式不一定會取用要求主體，例如伺服器傳回重新導向或404回應的 HTTP POST 要求。</span><span class="sxs-lookup"><span data-stu-id="a6646-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="a6646-109">在 HTTP POST 重新導向案例中：</span><span class="sxs-lookup"><span data-stu-id="a6646-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="a6646-110">用戶端可能已經傳送 POST 資料的一部分。</span><span class="sxs-lookup"><span data-stu-id="a6646-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="a6646-111">伺服器會寫入301回應。</span><span class="sxs-lookup"><span data-stu-id="a6646-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="a6646-112">此連接無法用於新的要求，直到來自前一個要求本文的 POST 資料完全讀取為止。</span><span class="sxs-lookup"><span data-stu-id="a6646-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="a6646-113">Kestrel 會嘗試清空要求主體。</span><span class="sxs-lookup"><span data-stu-id="a6646-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="a6646-114">清空要求主體表示在不處理資料的情況下讀取和捨棄資料。</span><span class="sxs-lookup"><span data-stu-id="a6646-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="a6646-115">清空程式可讓您在允許連線重複使用，以及清空任何剩餘資料所需的時間之間進行取捨：</span><span class="sxs-lookup"><span data-stu-id="a6646-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="a6646-116">清空有五秒的時間，無法設定。</span><span class="sxs-lookup"><span data-stu-id="a6646-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="a6646-117">如果或標頭所指定的所有資料在 `Content-Length` `Transfer-Encoding` 此時間之前未被讀取，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="a6646-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="a6646-118">有時您可能會想要在寫入回應之前或之後立即終止要求。</span><span class="sxs-lookup"><span data-stu-id="a6646-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="a6646-119">例如，用戶端可能會有限制性的資料上限。</span><span class="sxs-lookup"><span data-stu-id="a6646-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="a6646-120">限制上傳的資料可能是優先考慮。</span><span class="sxs-lookup"><span data-stu-id="a6646-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="a6646-121">在這種情況下，若要終止要求，請從控制器、頁面或中介軟體呼叫[HttpCoNtext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) 。 Razor</span><span class="sxs-lookup"><span data-stu-id="a6646-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="a6646-122">呼叫時有一些注意事項 `Abort` ：</span><span class="sxs-lookup"><span data-stu-id="a6646-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="a6646-123">建立新的連接可能很慢且昂貴。</span><span class="sxs-lookup"><span data-stu-id="a6646-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="a6646-124">在連接關閉之前，不保證用戶端已讀取回應。</span><span class="sxs-lookup"><span data-stu-id="a6646-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="a6646-125">呼叫 `Abort` 應該很罕見，而且會保留給嚴重的錯誤案例，而不是常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="a6646-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="a6646-126">只有 `Abort` 在需要解決特定問題時才呼叫。</span><span class="sxs-lookup"><span data-stu-id="a6646-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="a6646-127">例如， `Abort` 如果惡意用戶端嘗試張貼資料，或在用戶端程式代碼中發生錯誤，而導致大型或數個要求，則呼叫。</span><span class="sxs-lookup"><span data-stu-id="a6646-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="a6646-128">請勿呼叫 `Abort` 常見的錯誤狀況，例如 HTTP 404 (找不到) 。</span><span class="sxs-lookup"><span data-stu-id="a6646-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="a6646-129">在呼叫之前呼叫 [HttpResponse](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) ， `Abort` 可確保伺服器已完成寫入回應。</span><span class="sxs-lookup"><span data-stu-id="a6646-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="a6646-130">不過，用戶端行為不是可預測的，而且在中斷連接之前，可能不會讀取回應。</span><span class="sxs-lookup"><span data-stu-id="a6646-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="a6646-131">HTTP/2 的這個程式不同，因為通訊協定支援中止個別要求資料流程，而不需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="a6646-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="a6646-132">不適用五秒的清空超時時間。</span><span class="sxs-lookup"><span data-stu-id="a6646-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="a6646-133">如果在完成回應之後有任何未讀取的要求主體資料，則伺服器會傳送 HTTP/2 RST 框架。</span><span class="sxs-lookup"><span data-stu-id="a6646-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="a6646-134">系統會忽略其他要求主體資料框架。</span><span class="sxs-lookup"><span data-stu-id="a6646-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="a6646-135">可能的話，最好讓用戶端使用 [預期的： 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 要求標頭，並等待伺服器回應，然後再開始傳送要求本文。</span><span class="sxs-lookup"><span data-stu-id="a6646-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="a6646-136">這可讓用戶端有機會檢查回應，並在傳送不需要的資料之前中止。</span><span class="sxs-lookup"><span data-stu-id="a6646-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
