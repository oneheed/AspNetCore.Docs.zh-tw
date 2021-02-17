---
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
ms.openlocfilehash: 5c7412028a81233a0aedacfbe451014eeca4adc1
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552712"
---
<span data-ttu-id="f15bc-101">ASP.NET Core 提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="f15bc-101">ASP.NET Core provides the following benefits:</span></span>

* <span data-ttu-id="f15bc-102">用於建置 Web UI 和 Web API 的統一劇本。</span><span class="sxs-lookup"><span data-stu-id="f15bc-102">A unified story for building web UI and web APIs.</span></span>
* <span data-ttu-id="f15bc-103">可測試性架構。</span><span class="sxs-lookup"><span data-stu-id="f15bc-103">Architected for testability.</span></span>
* <span data-ttu-id="f15bc-104">[ Razor 頁面](xref:razor-pages/index)讓編碼頁面導向的案例更容易且更具生產力。</span><span class="sxs-lookup"><span data-stu-id="f15bc-104">[Razor Pages](xref:razor-pages/index) makes coding page-focused scenarios easier and more productive.</span></span>
* <span data-ttu-id="f15bc-105">[Blazor](xref:blazor/index) 可讓您在瀏覽器中使用 c # 與 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="f15bc-105">[Blazor](xref:blazor/index) lets you use C# in the browser alongside JavaScript.</span></span> <span data-ttu-id="f15bc-106">共用伺服器端與用戶端應用程式邏輯都是以 .NET 撰寫。</span><span class="sxs-lookup"><span data-stu-id="f15bc-106">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="f15bc-107">能夠在 Windows、macOS 和 Linux 上開發並執行。</span><span class="sxs-lookup"><span data-stu-id="f15bc-107">Ability to develop and run on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="f15bc-108">開放原始碼和以 [社區為焦點的](https://live.asp.net/)。</span><span class="sxs-lookup"><span data-stu-id="f15bc-108">Open-source and [community-focused](https://live.asp.net/).</span></span>
* <span data-ttu-id="f15bc-109">整合 [新式客戶](xref:blazor/index) 端架構和開發工作流程。</span><span class="sxs-lookup"><span data-stu-id="f15bc-109">Integration of [modern, client-side frameworks](xref:blazor/index) and development workflows.</span></span>
* <span data-ttu-id="f15bc-110">支援使用 [gRPC](xref:grpc/index)來裝載遠端程序呼叫 (RPC) 服務。</span><span class="sxs-lookup"><span data-stu-id="f15bc-110">Support for hosting Remote Procedure Call (RPC) services using [gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="f15bc-111">雲端就緒、以環境為基礎的[組態系統](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f15bc-111">A cloud-ready, environment-based [configuration system](xref:fundamentals/configuration/index).</span></span>
* <span data-ttu-id="f15bc-112">內建的[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f15bc-112">Built-in [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="f15bc-113">輕量、 [高效](https://github.com/aspnet/benchmarks)能且模組化的 HTTP 要求管線。</span><span class="sxs-lookup"><span data-stu-id="f15bc-113">A lightweight, [high-performance](https://github.com/aspnet/benchmarks), and modular HTTP request pipeline.</span></span>
* <span data-ttu-id="f15bc-114">能夠在下列情況下裝載：</span><span class="sxs-lookup"><span data-stu-id="f15bc-114">Ability to host on the following:</span></span>
  * [<span data-ttu-id="f15bc-115">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f15bc-115">Kestrel</span></span>](xref:fundamentals/servers/kestrel)
  * [<span data-ttu-id="f15bc-116">IIS</span><span class="sxs-lookup"><span data-stu-id="f15bc-116">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f15bc-117">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="f15bc-117">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys)
  * [<span data-ttu-id="f15bc-118">Nginx</span><span class="sxs-lookup"><span data-stu-id="f15bc-118">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f15bc-119">Apache</span><span class="sxs-lookup"><span data-stu-id="f15bc-119">Apache</span></span>](xref:host-and-deploy/linux-apache)
  * [<span data-ttu-id="f15bc-120">Docker</span><span class="sxs-lookup"><span data-stu-id="f15bc-120">Docker</span></span>](xref:host-and-deploy/docker/index)
* <span data-ttu-id="f15bc-121">[並存版本控制](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)。</span><span class="sxs-lookup"><span data-stu-id="f15bc-121">[Side-by-side versioning](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level).</span></span>
* <span data-ttu-id="f15bc-122">可簡化現代網頁程式開發的工具。</span><span class="sxs-lookup"><span data-stu-id="f15bc-122">Tooling that simplifies modern web development.</span></span>
