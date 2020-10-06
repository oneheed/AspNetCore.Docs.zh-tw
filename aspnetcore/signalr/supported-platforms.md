---
title: ASP.NET Core SignalR 支援的平臺
author: bradygaster
description: 瞭解 ASP.NET Core 支援的平臺 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754498"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="49b5f-103">ASP.NET Core SignalR 支援的平臺</span><span class="sxs-lookup"><span data-stu-id="49b5f-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="49b5f-104">伺服器系統需求</span><span class="sxs-lookup"><span data-stu-id="49b5f-104">Server system requirements</span></span>

<span data-ttu-id="49b5f-105">SignalR 針對 ASP.NET Core 支援 ASP.NET Core 支援的任何伺服器平臺。</span><span class="sxs-lookup"><span data-stu-id="49b5f-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="49b5f-106">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="49b5f-106">JavaScript client</span></span>

<span data-ttu-id="49b5f-107">[JavaScript 用戶端](xref:signalr/javascript-client)會在 NodeJS 8 和更新版本以及下列瀏覽器上執行：</span><span class="sxs-lookup"><span data-stu-id="49b5f-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="49b5f-108">瀏覽器</span><span class="sxs-lookup"><span data-stu-id="49b5f-108">Browser</span></span>                          | <span data-ttu-id="49b5f-109">版本</span><span class="sxs-lookup"><span data-stu-id="49b5f-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="49b5f-110">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="49b5f-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="49b5f-111">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="49b5f-111">Current&dagger;</span></span> |
| <span data-ttu-id="49b5f-112">Google Chrome （包括 Android）</span><span class="sxs-lookup"><span data-stu-id="49b5f-112">Google Chrome, including Android</span></span> | <span data-ttu-id="49b5f-113">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="49b5f-113">Current&dagger;</span></span> |
| <span data-ttu-id="49b5f-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="49b5f-114">Microsoft Edge</span></span>                   | <span data-ttu-id="49b5f-115">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="49b5f-115">Current&dagger;</span></span> |
| <span data-ttu-id="49b5f-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="49b5f-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="49b5f-117">當前&dagger;</span><span class="sxs-lookup"><span data-stu-id="49b5f-117">Current&dagger;</span></span> |

<span data-ttu-id="49b5f-118">&dagger;*Current* 指的是最新版的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="49b5f-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="49b5f-119">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="49b5f-119">.NET client</span></span>

<span data-ttu-id="49b5f-120">[.Net 用戶端](xref:signalr/dotnet-client)會在 ASP.NET Core 所支援的任何平臺上執行。</span><span class="sxs-lookup"><span data-stu-id="49b5f-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="49b5f-121">例如， [xamarin 開發人員可 SignalR ](https://github.com/aspnet/Announcements/issues/305)使用 xamarin. android 8.4.0.1 和更新版本，以及使用11.14.0.4 和更新版本的 ios 應用程式來建立 android 應用程式。</span><span class="sxs-lookup"><span data-stu-id="49b5f-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="49b5f-122">如果伺服器執行 IIS，則 Websocket 傳輸需要 Windows Server 2012 或更新版本上的 IIS 8.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="49b5f-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="49b5f-123">所有平臺都支援其他傳輸。</span><span class="sxs-lookup"><span data-stu-id="49b5f-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="49b5f-124">Java 用戶端</span><span class="sxs-lookup"><span data-stu-id="49b5f-124">Java client</span></span>

<span data-ttu-id="49b5f-125">[JAVA 用戶端](xref:signalr/java-client)支援 java 8 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="49b5f-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="49b5f-126">不支援的用戶端</span><span class="sxs-lookup"><span data-stu-id="49b5f-126">Unsupported clients</span></span>

<span data-ttu-id="49b5f-127">下列用戶端可供使用，但是實驗性或非官方的。</span><span class="sxs-lookup"><span data-stu-id="49b5f-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="49b5f-128">它們目前不受支援，而且可能永遠不會存在。</span><span class="sxs-lookup"><span data-stu-id="49b5f-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="49b5f-129">[C + + 用戶端](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="49b5f-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="49b5f-130">[Swift 用戶端](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="49b5f-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
