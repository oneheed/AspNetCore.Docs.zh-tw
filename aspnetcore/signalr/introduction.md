---
title: ASP.NET Core 簡介 SignalR
author: bradygaster
description: 瞭解 ASP.NET Core 連結 SignalR 庫如何簡化將即時功能新增至應用程式的程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: ab850fa8afbee9d2664868937362388a03374908
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634692"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="e7e59-103">ASP.NET Core 簡介 SignalR</span><span class="sxs-lookup"><span data-stu-id="e7e59-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="e7e59-104">什麼是 SignalR ？</span><span class="sxs-lookup"><span data-stu-id="e7e59-104">What is SignalR?</span></span>

<span data-ttu-id="e7e59-105">ASP.NET Core SignalR 是一個開放原始碼程式庫，可簡化將即時 web 功能新增至應用程式的程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="e7e59-106">即時 web 功能可讓伺服器端程式碼立即將內容推送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="e7e59-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="e7e59-107">適用于下列專案的絕佳候選項目 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="e7e59-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="e7e59-108">需要經常從伺服器取得更新的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="e7e59-109">例如遊戲、社交網路、投票、拍賣、地圖和 GPS 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="e7e59-110">儀表板和監視應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="e7e59-111">範例包括公司儀表板、即時銷售更新或旅行警示。</span><span class="sxs-lookup"><span data-stu-id="e7e59-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="e7e59-112">共同作業應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-112">Collaborative apps.</span></span> <span data-ttu-id="e7e59-113">共同作業應用程式的範例包括白板應用程式和小組會議軟體。</span><span class="sxs-lookup"><span data-stu-id="e7e59-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="e7e59-114">需要通知的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-114">Apps that require notifications.</span></span> <span data-ttu-id="e7e59-115">社交網路、電子郵件、交談、遊戲、旅行警示和其他使用通知的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="e7e59-116">SignalR 提供 API，可用於建立 (RPC) 的伺服器對用戶端 [遠端程序呼叫 ](https://wikipedia.org/wiki/Remote_procedure_call)。</span><span class="sxs-lookup"><span data-stu-id="e7e59-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="e7e59-117">這些 Rpc 會在用戶端上呼叫來自伺服器端 .NET Core 程式碼的 JavaScript 函式。</span><span class="sxs-lookup"><span data-stu-id="e7e59-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="e7e59-118">以下是 ASP.NET Core 的一些功能 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="e7e59-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="e7e59-119">自動處理連接管理。</span><span class="sxs-lookup"><span data-stu-id="e7e59-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="e7e59-120">同時將訊息傳送給所有已連線的用戶端。</span><span class="sxs-lookup"><span data-stu-id="e7e59-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="e7e59-121">例如，聊天室。</span><span class="sxs-lookup"><span data-stu-id="e7e59-121">For example, a chat room.</span></span>
* <span data-ttu-id="e7e59-122">將訊息傳送給特定用戶端或用戶端群組。</span><span class="sxs-lookup"><span data-stu-id="e7e59-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="e7e59-123">調整以處理增加的流量。</span><span class="sxs-lookup"><span data-stu-id="e7e59-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="e7e59-124">來源裝載于[ SignalR GitHub 上](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)的存放庫中。</span><span class="sxs-lookup"><span data-stu-id="e7e59-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="e7e59-125">傳輸</span><span class="sxs-lookup"><span data-stu-id="e7e59-125">Transports</span></span>

<span data-ttu-id="e7e59-126">SignalR 支援下列用來處理即時通訊 (的技巧，以順利回復) ：</span><span class="sxs-lookup"><span data-stu-id="e7e59-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="e7e59-127">WebSocket</span><span class="sxs-lookup"><span data-stu-id="e7e59-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="e7e59-128">伺服器傳送的事件</span><span class="sxs-lookup"><span data-stu-id="e7e59-128">Server-Sent Events</span></span>
* <span data-ttu-id="e7e59-129">長時間輪詢</span><span class="sxs-lookup"><span data-stu-id="e7e59-129">Long Polling</span></span>

<span data-ttu-id="e7e59-130">SignalR 自動選擇伺服器和用戶端功能內的最佳傳輸方法。</span><span class="sxs-lookup"><span data-stu-id="e7e59-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="e7e59-131">集線器</span><span class="sxs-lookup"><span data-stu-id="e7e59-131">Hubs</span></span>

<span data-ttu-id="e7e59-132">SignalR 使用 *中樞* 在用戶端與伺服器之間進行通訊。</span><span class="sxs-lookup"><span data-stu-id="e7e59-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="e7e59-133">中樞是一種高階管線，可讓用戶端和伺服器呼叫彼此的方法。</span><span class="sxs-lookup"><span data-stu-id="e7e59-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="e7e59-134">SignalR 會自動處理跨電腦界限的分派，讓用戶端可以在伺服器上呼叫方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="e7e59-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="e7e59-135">您可以將強型別參數傳遞給可啟用模型系結的方法。</span><span class="sxs-lookup"><span data-stu-id="e7e59-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="e7e59-136">SignalR 提供兩種內建的中樞通訊協定：以 JSON 為基礎的文字通訊協定，以及以 [MessagePack](https://msgpack.org/)為基礎的二進位通訊協定。</span><span class="sxs-lookup"><span data-stu-id="e7e59-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="e7e59-137">相較于 JSON，MessagePack 通常會建立較小的訊息。</span><span class="sxs-lookup"><span data-stu-id="e7e59-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="e7e59-138">舊版瀏覽器必須支援 [XHR 層級 2](https://caniuse.com/#feat=xhr2) ，才能提供 MessagePack 通訊協定支援。</span><span class="sxs-lookup"><span data-stu-id="e7e59-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="e7e59-139">中樞會傳送包含用戶端方法名稱和參數的訊息，以呼叫用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="e7e59-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="e7e59-140">傳送為方法參數的物件會使用設定的通訊協定來還原序列化。</span><span class="sxs-lookup"><span data-stu-id="e7e59-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="e7e59-141">用戶端會嘗試比對名稱與用戶端程式代碼中的方法。</span><span class="sxs-lookup"><span data-stu-id="e7e59-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="e7e59-142">當用戶端找到相符的時，它會呼叫方法並將已還原序列化的參數資料傳遞給它。</span><span class="sxs-lookup"><span data-stu-id="e7e59-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7e59-143">其他資源</span><span class="sxs-lookup"><span data-stu-id="e7e59-143">Additional resources</span></span>

* [<span data-ttu-id="e7e59-144">開始使用 SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7e59-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e7e59-145">支援的平臺</span><span class="sxs-lookup"><span data-stu-id="e7e59-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="e7e59-146">集線器</span><span class="sxs-lookup"><span data-stu-id="e7e59-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e7e59-147">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="e7e59-147">JavaScript client</span></span>](xref:signalr/javascript-client)
