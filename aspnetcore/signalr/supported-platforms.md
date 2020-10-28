---
title: 'ASP.NET Core :::no-loc(SignalR)::: 支援的平臺'
author: bradygaster
description: '瞭解 ASP.NET Core 支援的平臺 :::no-loc(SignalR)::: 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
no-loc:
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
uid: signalr/supported-platforms
ms.openlocfilehash: 761edbbe7bab28d2340207a0adea0718b37c7ec1
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690340"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="2b7df-103">ASP.NET Core :::no-loc(SignalR)::: 支援的平臺</span><span class="sxs-lookup"><span data-stu-id="2b7df-103">ASP.NET Core :::no-loc(SignalR)::: supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="2b7df-104">伺服器系統需求</span><span class="sxs-lookup"><span data-stu-id="2b7df-104">Server system requirements</span></span>

<span data-ttu-id="2b7df-105">:::no-loc(SignalR)::: 針對 ASP.NET Core 支援 ASP.NET Core 支援的任何伺服器平臺。</span><span class="sxs-lookup"><span data-stu-id="2b7df-105">:::no-loc(SignalR)::: for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="2b7df-106">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="2b7df-106">JavaScript client</span></span>

<span data-ttu-id="2b7df-107">[JavaScript 用戶端](xref:signalr/javascript-client)會在 NodeJS 8 和更新版本以及下列瀏覽器上執行：</span><span class="sxs-lookup"><span data-stu-id="2b7df-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="2b7df-108">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="2b7df-108">Browser</span></span>                          | <span data-ttu-id="2b7df-109">版本</span><span class="sxs-lookup"><span data-stu-id="2b7df-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="2b7df-110">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="2b7df-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="2b7df-111">目前&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b7df-111">Current&dagger;</span></span> |
| <span data-ttu-id="2b7df-112">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="2b7df-112">Google Chrome, including Android</span></span> | <span data-ttu-id="2b7df-113">目前&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b7df-113">Current&dagger;</span></span> |
| <span data-ttu-id="2b7df-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="2b7df-114">Microsoft Edge</span></span>                   | <span data-ttu-id="2b7df-115">目前&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b7df-115">Current&dagger;</span></span> |
| <span data-ttu-id="2b7df-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="2b7df-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="2b7df-117">目前&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b7df-117">Current&dagger;</span></span> |

<span data-ttu-id="2b7df-118">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="2b7df-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="2b7df-119">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="2b7df-119">.NET client</span></span>

<span data-ttu-id="2b7df-120">[.Net 用戶端](xref:signalr/dotnet-client)會在 ASP.NET Core 所支援的任何平臺上執行。</span><span class="sxs-lookup"><span data-stu-id="2b7df-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="2b7df-121">例如， [xamarin 開發人員可 :::no-loc(SignalR)::: ](https://github.com/aspnet/Announcements/issues/305)使用 xamarin. android 8.4.0.1 和更新版本，以及使用11.14.0.4 和更新版本的 ios 應用程式來建立 android 應用程式。</span><span class="sxs-lookup"><span data-stu-id="2b7df-121">For example, [Xamarin developers can use :::no-loc(SignalR):::](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="2b7df-122">如果伺服器執行 IIS，則 Websocket 傳輸需要 Windows Server 2012 或更新版本上的 IIS 8.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="2b7df-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="2b7df-123">所有平臺都支援其他傳輸。</span><span class="sxs-lookup"><span data-stu-id="2b7df-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="2b7df-124">Java 用戶端</span><span class="sxs-lookup"><span data-stu-id="2b7df-124">Java client</span></span>

<span data-ttu-id="2b7df-125">[JAVA 用戶端](xref:signalr/java-client)支援 java 8 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="2b7df-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="2b7df-126">不支援的用戶端</span><span class="sxs-lookup"><span data-stu-id="2b7df-126">Unsupported clients</span></span>

<span data-ttu-id="2b7df-127">下列用戶端可供使用，但是實驗性或非官方的。</span><span class="sxs-lookup"><span data-stu-id="2b7df-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="2b7df-128">它們目前不受支援，而且可能永遠不會存在。</span><span class="sxs-lookup"><span data-stu-id="2b7df-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="2b7df-129">[C + + 用戶端](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="2b7df-129">[C++ client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span></span>

* <span data-ttu-id="2b7df-130">[Swift 用戶端](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="2b7df-130">[Swift client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span></span>
