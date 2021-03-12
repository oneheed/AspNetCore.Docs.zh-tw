---
title: ASP.NET Core 簡介
author: rick-anderson
description: 取得 ASP.NET Core 的簡介，這是一種跨平臺、高效能的開放原始碼架構，可用於建立現代化、已啟用雲端的、網際網路連線的應用程式。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: 3e41336d084e25319f8b1ab4c4ab3175b758d23d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588798"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="a8269-103">ASP.NET Core 簡介</span><span class="sxs-lookup"><span data-stu-id="a8269-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="a8269-104">由 [Daniel Roth](https://github.com/danroth27)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Shaun Luttin](https://twitter.com/dicshaunary) 提供</span><span class="sxs-lookup"><span data-stu-id="a8269-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8269-105">ASP.NET Core 是跨平臺、高效能的 [開放原始](https://github.com/dotnet/aspnetcore) 碼架構，可用於建立現代化、已啟用雲端的、網際網路連線的應用程式。</span><span class="sxs-lookup"><span data-stu-id="a8269-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="a8269-106">利用 ASP.NET Core，您可以：</span><span class="sxs-lookup"><span data-stu-id="a8269-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="a8269-107">建立 web 應用程式和服務、 [物聯網 (IoT) ](https://www.microsoft.com/internet-of-things/) 應用程式和行動後端。</span><span class="sxs-lookup"><span data-stu-id="a8269-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="a8269-108">在 Windows、macOS 和 Linux 上使用您最愛的開發工具。</span><span class="sxs-lookup"><span data-stu-id="a8269-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8269-109">部署到雲端或在內部部署。</span><span class="sxs-lookup"><span data-stu-id="a8269-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="a8269-110">在 [.Net Core](/dotnet/core/introduction)上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="a8269-111">為什麼要選擇 ASP.NET Core？</span><span class="sxs-lookup"><span data-stu-id="a8269-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="a8269-112">數百萬名開發人員使用或使用 [ASP.NET](/aspnet/overview) 4.x 來建立 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="a8269-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="a8269-113">ASP.NET Core 是 ASP.NET 4.x 的重新設計，包括架構變更，這些變更會導致更精簡、更模組化的架構。</span><span class="sxs-lookup"><span data-stu-id="a8269-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="a8269-114">使用 ASP.NET Core MVC 建置 Web API 和 Web UI</span><span class="sxs-lookup"><span data-stu-id="a8269-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="a8269-115">ASP.NET Core MVC 提供了建置 [Web API](xref:tutorials/first-web-api) 和 [Web 應用程式](xref:tutorials/razor-pages/index)的功能：</span><span class="sxs-lookup"><span data-stu-id="a8269-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="a8269-116">[模型檢視控制器 (MVC) 模式](xref:mvc/overview)有助於讓您的 Web API 和 Web 應用程式可測試。</span><span class="sxs-lookup"><span data-stu-id="a8269-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="a8269-117">[ Razor Pages](xref:razor-pages/index)是以頁面為基礎的程式設計模型，可讓您更輕鬆且更具生產力地建立 web UI。</span><span class="sxs-lookup"><span data-stu-id="a8269-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="a8269-118">[ Razor 標記](xref:mvc/views/razor)可提供[ Razor 頁面](xref:razor-pages/index)和[MVC 視圖](xref:mvc/views/overview)的生產力語法。</span><span class="sxs-lookup"><span data-stu-id="a8269-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="a8269-119">[標記](xref:mvc/views/tag-helpers/intro) 協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="a8269-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="a8269-120">[多個資料格式和內容交涉](xref:web-api/advanced/formatting)的內建支援可讓您的 Web API 連線到各種用戶端，包括瀏覽器和行動裝置。</span><span class="sxs-lookup"><span data-stu-id="a8269-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="a8269-121">[模型繫結](xref:mvc/models/model-binding)會自動將 HTTP 要求中的資料對應至動作方法參數。</span><span class="sxs-lookup"><span data-stu-id="a8269-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="a8269-122">[模型驗證](xref:mvc/models/validation)會自動執行用戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="a8269-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="a8269-123">用戶端開發</span><span class="sxs-lookup"><span data-stu-id="a8269-123">Client-side development</span></span>

<span data-ttu-id="a8269-124">ASP.NET Core 與熱門的用戶端架構和程式庫緊密整合，包括 [Blazor](xref:blazor/index) 、 [角度](xref:spa/angular)、 [回應](xref:spa/react)及 [啟動](https://getbootstrap.com/)程式。</span><span class="sxs-lookup"><span data-stu-id="a8269-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="a8269-125">如需詳細資訊，請參閱 <xref:blazor/index> 及 *用戶端開發* 下的相關主題。</span><span class="sxs-lookup"><span data-stu-id="a8269-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="a8269-126">ASP.NET 核心目標 framework</span><span class="sxs-lookup"><span data-stu-id="a8269-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="a8269-127">ASP.NET Core 3.x 和更新版本只能以 .NET Core 為目標。</span><span class="sxs-lookup"><span data-stu-id="a8269-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="a8269-128">一般而言，ASP.NET Core 是由 [.Net Standard](/dotnet/standard/net-standard) 程式庫所組成。</span><span class="sxs-lookup"><span data-stu-id="a8269-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="a8269-129">使用 .NET Standard 2.0 撰寫的程式庫可在[任何實作 .NET Standard 2.0 的 .NET 平台](/dotnet/standard/net-standard#net-implementation-support)上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="a8269-130">將目標指向 .NET Core 有多個好處，而這些好處也隨著版本更新越來越多。</span><span class="sxs-lookup"><span data-stu-id="a8269-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="a8269-131">NET Core 較 .NET Framework 多的好處包含：</span><span class="sxs-lookup"><span data-stu-id="a8269-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="a8269-132">跨平台。</span><span class="sxs-lookup"><span data-stu-id="a8269-132">Cross-platform.</span></span> <span data-ttu-id="a8269-133">在 Windows、macOS 和 Linux 上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8269-134">提升效能</span><span class="sxs-lookup"><span data-stu-id="a8269-134">Improved performance</span></span>
* [<span data-ttu-id="a8269-135">並存版本控制</span><span class="sxs-lookup"><span data-stu-id="a8269-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="a8269-136">新的 API</span><span class="sxs-lookup"><span data-stu-id="a8269-136">New APIs</span></span>
* <span data-ttu-id="a8269-137">開放原始碼</span><span class="sxs-lookup"><span data-stu-id="a8269-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="a8269-138">建議學習路徑</span><span class="sxs-lookup"><span data-stu-id="a8269-138">Recommended learning path</span></span>

<span data-ttu-id="a8269-139">針對開發 ASP.NET Core 應用程式的簡介，我們建議使用下列一系列的教學課程：</span><span class="sxs-lookup"><span data-stu-id="a8269-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="a8269-140">遵循您想要開發或維護之應用程式類型的教學課程。</span><span class="sxs-lookup"><span data-stu-id="a8269-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="a8269-141">應用程式類型</span><span class="sxs-lookup"><span data-stu-id="a8269-141">App type</span></span>  |<span data-ttu-id="a8269-142">狀況</span><span class="sxs-lookup"><span data-stu-id="a8269-142">Scenario</span></span>  |<span data-ttu-id="a8269-143">教學課程</span><span class="sxs-lookup"><span data-stu-id="a8269-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="a8269-144">Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-144">Web app</span></span>                   | <span data-ttu-id="a8269-145">新的伺服器端 web UI 開發</span><span class="sxs-lookup"><span data-stu-id="a8269-145">New server-side web UI development</span></span> |[<span data-ttu-id="a8269-146">開始使用 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="a8269-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="a8269-147">Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-147">Web app</span></span>                   | <span data-ttu-id="a8269-148">維護 MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="a8269-149">開始使用 MVC</span><span class="sxs-lookup"><span data-stu-id="a8269-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="a8269-150">Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-150">Web app</span></span>                   | <span data-ttu-id="a8269-151">用戶端 web UI 開發</span><span class="sxs-lookup"><span data-stu-id="a8269-151">Client-side web UI development</span></span> |[<span data-ttu-id="a8269-152">入門 Blazor</span><span class="sxs-lookup"><span data-stu-id="a8269-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="a8269-153">Web API</span><span class="sxs-lookup"><span data-stu-id="a8269-153">Web API</span></span>                   | <span data-ttu-id="a8269-154">RESTful HTTP 服務</span><span class="sxs-lookup"><span data-stu-id="a8269-154">RESTful HTTP services</span></span> |<span data-ttu-id="a8269-155">[建立 web API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="a8269-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="a8269-156">遠端程序呼叫應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-156">Remote Procedure Call app</span></span> | <span data-ttu-id="a8269-157">使用通訊協定緩衝區的合約優先服務</span><span class="sxs-lookup"><span data-stu-id="a8269-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="a8269-158">開始使用 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="a8269-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="a8269-159">即時應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-159">Real-time app</span></span>             | <span data-ttu-id="a8269-160">伺服器和連線用戶端之間的雙向通訊</span><span class="sxs-lookup"><span data-stu-id="a8269-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="a8269-161">入門 SignalR</span><span class="sxs-lookup"><span data-stu-id="a8269-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="a8269-162">遵循示範如何進行基本資料存取的教學課程。</span><span class="sxs-lookup"><span data-stu-id="a8269-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="a8269-163">狀況</span><span class="sxs-lookup"><span data-stu-id="a8269-163">Scenario</span></span>  |<span data-ttu-id="a8269-164">教學課程</span><span class="sxs-lookup"><span data-stu-id="a8269-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="a8269-165">新開發</span><span class="sxs-lookup"><span data-stu-id="a8269-165">New development</span></span>        |[<span data-ttu-id="a8269-166">Razor 使用 Entity Framework Core 的頁面</span><span class="sxs-lookup"><span data-stu-id="a8269-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="a8269-167">維護 MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="a8269-168">搭配 Entity Framework Core 的 MVC</span><span class="sxs-lookup"><span data-stu-id="a8269-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="a8269-169">閱讀適用于所有應用程式類型的 ASP.NET 核心 [基礎](xref:fundamentals/index) 概念總覽。</span><span class="sxs-lookup"><span data-stu-id="a8269-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="a8269-170">流覽目錄以尋找感興趣的其他主題。</span><span class="sxs-lookup"><span data-stu-id="a8269-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="a8269-171">&dagger;另外還有 [互動式 WEB API 教學](/learn/modules/build-web-api-net-core)課程。</span><span class="sxs-lookup"><span data-stu-id="a8269-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="a8269-172">不需要本機安裝開發工具。</span><span class="sxs-lookup"><span data-stu-id="a8269-172">No local installation of development tools is required.</span></span> <span data-ttu-id="a8269-173">程式碼會在您的瀏覽器中于 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 中執行，並使用 [捲曲](https://curl.haxx.se/) 來進行測試。</span><span class="sxs-lookup"><span data-stu-id="a8269-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="a8269-174">從 .NET Framework 遷移</span><span class="sxs-lookup"><span data-stu-id="a8269-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="a8269-175">如需將 ASP.NET 4.x 應用程式遷移至 ASP.NET Core 的參考指南，請參閱 <xref:migration/proper-to-2x/index> 。</span><span class="sxs-lookup"><span data-stu-id="a8269-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8269-176">ASP.NET Core 是跨平臺、高效能的 [開放原始](https://github.com/dotnet/aspnetcore) 碼架構，可用於建立現代化、已啟用雲端的、網際網路連線的應用程式。</span><span class="sxs-lookup"><span data-stu-id="a8269-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="a8269-177">利用 ASP.NET Core，您可以：</span><span class="sxs-lookup"><span data-stu-id="a8269-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="a8269-178">建立 web 應用程式和服務、 [物聯網 (IoT) ](https://www.microsoft.com/internet-of-things/) 應用程式和行動後端。</span><span class="sxs-lookup"><span data-stu-id="a8269-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="a8269-179">在 Windows、macOS 和 Linux 上使用您最愛的開發工具。</span><span class="sxs-lookup"><span data-stu-id="a8269-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8269-180">部署到雲端或在內部部署。</span><span class="sxs-lookup"><span data-stu-id="a8269-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="a8269-181">在 [.NET Core 或 .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) 上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="a8269-182">為什麼要選擇 ASP.NET Core？</span><span class="sxs-lookup"><span data-stu-id="a8269-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="a8269-183">數百萬名開發人員使用或使用 [ASP.NET](/aspnet/overview) 4.x 來建立 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="a8269-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="a8269-184">ASP.NET Core 是 ASP.NET 4.x 的重新設計，其架構變更可產生更為精簡且更加模組化的架構。</span><span class="sxs-lookup"><span data-stu-id="a8269-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="a8269-185">使用 ASP.NET Core MVC 建置 Web API 和 Web UI</span><span class="sxs-lookup"><span data-stu-id="a8269-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="a8269-186">ASP.NET Core MVC 提供了建置 [Web API](xref:tutorials/first-web-api) 和 [Web 應用程式](xref:tutorials/razor-pages/index)的功能：</span><span class="sxs-lookup"><span data-stu-id="a8269-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="a8269-187">[模型檢視控制器 (MVC) 模式](xref:mvc/overview)有助於讓您的 Web API 和 Web 應用程式可測試。</span><span class="sxs-lookup"><span data-stu-id="a8269-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="a8269-188">[ Razor Pages](xref:razor-pages/index)是以頁面為基礎的程式設計模型，可讓您更輕鬆且更具生產力地建立 web UI。</span><span class="sxs-lookup"><span data-stu-id="a8269-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="a8269-189">[ Razor 標記](xref:mvc/views/razor)可提供[ Razor 頁面](xref:razor-pages/index)和[MVC 視圖](xref:mvc/views/overview)的生產力語法。</span><span class="sxs-lookup"><span data-stu-id="a8269-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="a8269-190">[標記](xref:mvc/views/tag-helpers/intro) 協助程式可讓伺服器端程式碼參與建立和轉譯檔案中的 HTML 元素 Razor 。</span><span class="sxs-lookup"><span data-stu-id="a8269-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="a8269-191">[多個資料格式和內容交涉](xref:web-api/advanced/formatting)的內建支援可讓您的 Web API 連線到各種用戶端，包括瀏覽器和行動裝置。</span><span class="sxs-lookup"><span data-stu-id="a8269-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="a8269-192">[模型繫結](xref:mvc/models/model-binding)會自動將 HTTP 要求中的資料對應至動作方法參數。</span><span class="sxs-lookup"><span data-stu-id="a8269-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="a8269-193">[模型驗證](xref:mvc/models/validation)會自動執行用戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="a8269-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="a8269-194">用戶端開發</span><span class="sxs-lookup"><span data-stu-id="a8269-194">Client-side development</span></span>

<span data-ttu-id="a8269-195">ASP.NET Core 與熱門的用戶端架構和程式庫緊密整合，包括 [Blazor](xref:blazor/index) 、 [角度](xref:spa/angular)、 [回應](xref:spa/react)及 [啟動](https://getbootstrap.com/)程式。</span><span class="sxs-lookup"><span data-stu-id="a8269-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="a8269-196">如需詳細資訊，請參閱 <xref:blazor/index> 及 *用戶端開發* 下的相關主題。</span><span class="sxs-lookup"><span data-stu-id="a8269-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="a8269-197">將目標指向 .NET Framework 的 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8269-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="a8269-198">ASP.NET Core 2.x 的目標可以是 NET Core 或 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a8269-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="a8269-199">將目標指向 .NET Framework 的 ASP.NET Core 應用程式無法跨平台&mdash;而只能在 Windows 上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="a8269-200">ASP.NET Core 2.x 通常會包含 [.NET Standard](/dotnet/standard/net-standard) 程式庫。</span><span class="sxs-lookup"><span data-stu-id="a8269-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="a8269-201">使用 .NET Standard 2.0 撰寫的程式庫可在[任何實作 .NET Standard 2.0 的 .NET 平台](/dotnet/standard/net-standard#net-implementation-support)上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="a8269-202">實作 .NET Standard 2.0 的 .NET Framework 版本支援 ASP.NET Core 2.x：</span><span class="sxs-lookup"><span data-stu-id="a8269-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="a8269-203">建議使用 .NET Framework 最新版本。</span><span class="sxs-lookup"><span data-stu-id="a8269-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="a8269-204">.NET Framework 4.6.1 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="a8269-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="a8269-205">ASP.NET Core 3.0 及更新版本只可在.NET Core 上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="a8269-206">如需此變更的詳細資料，請參閱[A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (搶先看 ASP.NET Core 3.0 的變更)。</span><span class="sxs-lookup"><span data-stu-id="a8269-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="a8269-207">將目標指向 .NET Core 有多個好處，而這些好處也隨著版本更新越來越多。</span><span class="sxs-lookup"><span data-stu-id="a8269-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="a8269-208">NET Core 較 .NET Framework 多的好處包含：</span><span class="sxs-lookup"><span data-stu-id="a8269-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="a8269-209">跨平台。</span><span class="sxs-lookup"><span data-stu-id="a8269-209">Cross-platform.</span></span> <span data-ttu-id="a8269-210">可在 macOS、Linux 及 Windows 上執行。</span><span class="sxs-lookup"><span data-stu-id="a8269-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="a8269-211">提升效能</span><span class="sxs-lookup"><span data-stu-id="a8269-211">Improved performance</span></span>
* [<span data-ttu-id="a8269-212">並存版本控制</span><span class="sxs-lookup"><span data-stu-id="a8269-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="a8269-213">新的 API</span><span class="sxs-lookup"><span data-stu-id="a8269-213">New APIs</span></span>
* <span data-ttu-id="a8269-214">開放原始碼</span><span class="sxs-lookup"><span data-stu-id="a8269-214">Open source</span></span>

<span data-ttu-id="a8269-215">為了協助關閉從 .NET Framework 到 .NET Core 的 API 間距， [Windows 相容性套件](/dotnet/core/porting/windows-compat-pack) 提供了數千個僅限 Windows 的 api，可在 .net core 中使用。</span><span class="sxs-lookup"><span data-stu-id="a8269-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="a8269-216">這些 API 並不適用於 .NET Core 1.x。</span><span class="sxs-lookup"><span data-stu-id="a8269-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="a8269-217">建議學習路徑</span><span class="sxs-lookup"><span data-stu-id="a8269-217">Recommended learning path</span></span>

<span data-ttu-id="a8269-218">我們建議遵循一系列的教學課程和文章，取得開發 ASP.NET Core 應用程式的簡介：</span><span class="sxs-lookup"><span data-stu-id="a8269-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="a8269-219">遵循您想要開發或維護的應用程式類型的教學課程。</span><span class="sxs-lookup"><span data-stu-id="a8269-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="a8269-220">應用程式類型</span><span class="sxs-lookup"><span data-stu-id="a8269-220">App type</span></span>  |<span data-ttu-id="a8269-221">狀況</span><span class="sxs-lookup"><span data-stu-id="a8269-221">Scenario</span></span>  |<span data-ttu-id="a8269-222">教學課程</span><span class="sxs-lookup"><span data-stu-id="a8269-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="a8269-223">Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-223">Web app</span></span>                   | <span data-ttu-id="a8269-224">針對全新開發</span><span class="sxs-lookup"><span data-stu-id="a8269-224">For new development</span></span>        |[<span data-ttu-id="a8269-225">開始使用 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="a8269-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="a8269-226">Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-226">Web app</span></span>                   | <span data-ttu-id="a8269-227">針對維護 MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="a8269-228">開始使用 MVC</span><span class="sxs-lookup"><span data-stu-id="a8269-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="a8269-229">Web API</span><span class="sxs-lookup"><span data-stu-id="a8269-229">Web API</span></span>                   |                            |<span data-ttu-id="a8269-230">[建立 web API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="a8269-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="a8269-231">即時應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-231">Real-time app</span></span>             |                            |[<span data-ttu-id="a8269-232">入門 SignalR</span><span class="sxs-lookup"><span data-stu-id="a8269-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="a8269-233">遵循示範如何進行基本資料存取的教學課程。</span><span class="sxs-lookup"><span data-stu-id="a8269-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="a8269-234">狀況</span><span class="sxs-lookup"><span data-stu-id="a8269-234">Scenario</span></span>  |<span data-ttu-id="a8269-235">教學課程</span><span class="sxs-lookup"><span data-stu-id="a8269-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="a8269-236">針對全新開發</span><span class="sxs-lookup"><span data-stu-id="a8269-236">For new development</span></span>        |[<span data-ttu-id="a8269-237">Razor 使用 Entity Framework Core 的頁面</span><span class="sxs-lookup"><span data-stu-id="a8269-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="a8269-238">針對維護 MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="a8269-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="a8269-239">搭配 Entity Framework Core 的 MVC</span><span class="sxs-lookup"><span data-stu-id="a8269-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="a8269-240">閱讀適用于所有應用程式類型的 ASP.NET 核心 [基礎](xref:fundamentals/index) 概念總覽。</span><span class="sxs-lookup"><span data-stu-id="a8269-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="a8269-241">瀏覽其他您感興趣主題的目錄。</span><span class="sxs-lookup"><span data-stu-id="a8269-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="a8269-242">&dagger;另外還有一個 [WEB API 教學課程，您可以完全在瀏覽器中執行](/learn/modules/build-web-api-net-core)，而不需要本機 IDE 安裝。</span><span class="sxs-lookup"><span data-stu-id="a8269-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="a8269-243">程式碼會在 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 中執行，且會使用 [curl](https://curl.haxx.se/) 來進行測試。</span><span class="sxs-lookup"><span data-stu-id="a8269-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="a8269-244">從 .NET Framework 遷移</span><span class="sxs-lookup"><span data-stu-id="a8269-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="a8269-245">如需將 ASP.NET 應用程式遷移至 ASP.NET Core 的參考指南，請參閱 <xref:migration/proper-to-2x/index> 。</span><span class="sxs-lookup"><span data-stu-id="a8269-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="a8269-246">如何下載範例</span><span class="sxs-lookup"><span data-stu-id="a8269-246">How to download a sample</span></span>

<span data-ttu-id="a8269-247">許多文章及教學課程都有包含範例程式碼的連結。</span><span class="sxs-lookup"><span data-stu-id="a8269-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="a8269-248">[下載 ASP.NET 存放庫 ZIP 檔案](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/main)。</span><span class="sxs-lookup"><span data-stu-id="a8269-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/main).</span></span>
1. <span data-ttu-id="a8269-249">將 *Docs-main.zip* 檔案解壓縮。</span><span class="sxs-lookup"><span data-stu-id="a8269-249">Unzip the *Docs-main.zip* file.</span></span>
1. <span data-ttu-id="a8269-250">使用範例連結中的 URL，協助您巡覽至範例目錄。</span><span class="sxs-lookup"><span data-stu-id="a8269-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="a8269-251">範例程式碼中的前置處理器指示詞</span><span class="sxs-lookup"><span data-stu-id="a8269-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="a8269-252">為了示範多個案例，範例應用程式會使用 `#define` 和預處理器指示詞， `#if-#else/#elif-#endif` 選擇性地編譯及執行範例程式碼的不同區段。</span><span class="sxs-lookup"><span data-stu-id="a8269-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="a8269-253">針對使用這種方法的範例，請在 c # 檔案頂端設定指示詞， `#define` 以定義與您想要執行的情節相關聯的符號。</span><span class="sxs-lookup"><span data-stu-id="a8269-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="a8269-254">某些範例需要在多個檔案的頂端定義符號，才能執行案例。</span><span class="sxs-lookup"><span data-stu-id="a8269-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="a8269-255">例如下列 `#define` 符號清單指出其提供四個情節 (每個符號一個情節)。</span><span class="sxs-lookup"><span data-stu-id="a8269-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="a8269-256">目前的範例設定會執行 `TemplateCode` 情節：</span><span class="sxs-lookup"><span data-stu-id="a8269-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="a8269-257">若要變更此範例，以執行 `ExpandDefault` 情節，請定義 `ExpandDefault` 符號，並將剩餘的符號設為註解：</span><span class="sxs-lookup"><span data-stu-id="a8269-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="a8269-258">如需如何使用 [C# 前置處理器指示詞](/dotnet/csharp/language-reference/preprocessor-directives/)，以選擇編譯不同之程式碼區段的詳細資訊，請參閱 [#define (C# 參考)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) 及 [#if (C# 參考)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)。</span><span class="sxs-lookup"><span data-stu-id="a8269-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="a8269-259">範例程式碼中的區域</span><span class="sxs-lookup"><span data-stu-id="a8269-259">Regions in sample code</span></span>

<span data-ttu-id="a8269-260">某些範例應用程式包含 [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) 和 [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) c # 指示詞所括住的程式碼區段。</span><span class="sxs-lookup"><span data-stu-id="a8269-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="a8269-261">文件建置系統會將這些區域插入轉譯的文件主題中。</span><span class="sxs-lookup"><span data-stu-id="a8269-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="a8269-262">區域名稱通常包含字組 "snippet"。</span><span class="sxs-lookup"><span data-stu-id="a8269-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="a8269-263">下列範例顯示了名為 `snippet_WebHostDefaults` 的區域：</span><span class="sxs-lookup"><span data-stu-id="a8269-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="a8269-264">上述的 C# 程式碼片段會在主題的 Markdown 檔案中，透過以下程式行加以參考：</span><span class="sxs-lookup"><span data-stu-id="a8269-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="a8269-265">您可以放心地忽略 (或移除 `#region` 程式碼) 的和 `#endregion` 指示詞。</span><span class="sxs-lookup"><span data-stu-id="a8269-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="a8269-266">如果您打算執行主題中所述的範例案例，請不要改變這些指示詞內的程式碼。</span><span class="sxs-lookup"><span data-stu-id="a8269-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="a8269-267">您可以在試驗其他案例時自由改變程式碼。</span><span class="sxs-lookup"><span data-stu-id="a8269-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="a8269-268">如需詳細資訊，請參閱 [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/main/CONTRIBUTING.md#code-snippets) (參與 ASP.NET 文件：程式碼片段)。</span><span class="sxs-lookup"><span data-stu-id="a8269-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/main/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="a8269-269">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a8269-269">Next steps</span></span>

<span data-ttu-id="a8269-270">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="a8269-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="a8269-271">ASP.NET Core 基本概念</span><span class="sxs-lookup"><span data-stu-id="a8269-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="a8269-272">[每週的 ASP.NET 社群之聲](https://live.asp.net/) \(英文\) 涵蓋了小組的進度和計劃，</span><span class="sxs-lookup"><span data-stu-id="a8269-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="a8269-273">並提供新的部落格和協力廠商軟體。</span><span class="sxs-lookup"><span data-stu-id="a8269-273">It features new blogs and third-party software.</span></span>
