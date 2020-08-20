---
title: ASP.NET Core 負載/壓力測試
author: Jeremy-Meng
description: 瞭解 ASP.NET Core 應用程式的負載測試和壓力測試有幾個值得注意的工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 8e3ca41312922cbf44361601c38e455b342e9fe1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632807"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="a3e38-103">ASP.NET Core 負載/壓力測試</span><span class="sxs-lookup"><span data-stu-id="a3e38-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="a3e38-104">負載測試和壓力測試很重要，可確保 web 應用程式的效能和可調整性。</span><span class="sxs-lookup"><span data-stu-id="a3e38-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="a3e38-105">雖然它們通常會共用類似的測試，但它們的目標是不同的。</span><span class="sxs-lookup"><span data-stu-id="a3e38-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="a3e38-106">**負載測試**：測試應用程式是否可在特定案例中處理指定的使用者負載，同時仍滿足回應目標。</span><span class="sxs-lookup"><span data-stu-id="a3e38-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="a3e38-107">應用程式會在正常情況下執行。</span><span class="sxs-lookup"><span data-stu-id="a3e38-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="a3e38-108">**壓力測試**：在極端情況下執行時測試應用程式穩定性，通常很長一段時間。</span><span class="sxs-lookup"><span data-stu-id="a3e38-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="a3e38-109">這些測試會在應用程式上放置高度的使用者負載，可能會尖峰或逐漸增加負載，或限制應用程式的計算資源。</span><span class="sxs-lookup"><span data-stu-id="a3e38-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="a3e38-110">壓力測試會判斷壓力下的應用程式是否可以從失敗中復原，並正常地返回預期的行為。</span><span class="sxs-lookup"><span data-stu-id="a3e38-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="a3e38-111">在壓力下，應用程式不會在正常情況下執行。</span><span class="sxs-lookup"><span data-stu-id="a3e38-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="a3e38-112">Visual Studio 2019 宣佈計畫 [取代負載測試](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)。</span><span class="sxs-lookup"><span data-stu-id="a3e38-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="a3e38-113">對應的 Azure DevOps 雲端負載測試服務已關閉。</span><span class="sxs-lookup"><span data-stu-id="a3e38-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="a3e38-114">協力廠商工具</span><span class="sxs-lookup"><span data-stu-id="a3e38-114">Third-party tools</span></span>

<span data-ttu-id="a3e38-115">下列清單包含具有各種功能集的協力廠商 web 效能工具：</span><span class="sxs-lookup"><span data-stu-id="a3e38-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="a3e38-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="a3e38-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="a3e38-117">ApacheBench (ab) </span><span class="sxs-lookup"><span data-stu-id="a3e38-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="a3e38-118">加特林</span><span class="sxs-lookup"><span data-stu-id="a3e38-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="a3e38-119">k6</span><span class="sxs-lookup"><span data-stu-id="a3e38-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="a3e38-120">蝗蟲</span><span class="sxs-lookup"><span data-stu-id="a3e38-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="a3e38-121">West 風 WebSurge</span><span class="sxs-lookup"><span data-stu-id="a3e38-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="a3e38-122">Netling</span><span class="sxs-lookup"><span data-stu-id="a3e38-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="a3e38-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="a3e38-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)