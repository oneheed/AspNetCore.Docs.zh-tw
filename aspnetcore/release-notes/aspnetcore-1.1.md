---
title: ASP.NET Core 1.1 的新功能
author: rick-anderson
description: 了解 ASP.NET Core 1.1 的新功能。
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: cc891280a6314dbc4c0838d5b4a8d2a20698eab6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059737"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="a0f4d-103">ASP.NET Core 1.1 的新功能</span><span class="sxs-lookup"><span data-stu-id="a0f4d-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="a0f4d-104">ASP.NET Core 1.1 包含下列新功能：</span><span class="sxs-lookup"><span data-stu-id="a0f4d-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="a0f4d-105">URL 重寫中介軟體</span><span class="sxs-lookup"><span data-stu-id="a0f4d-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="a0f4d-106">回應快取中介軟體</span><span class="sxs-lookup"><span data-stu-id="a0f4d-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="a0f4d-107">檢視元件作為標記協助程式</span><span class="sxs-lookup"><span data-stu-id="a0f4d-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="a0f4d-108">中介軟體作為 MVC 篩選條件</span><span class="sxs-lookup"><span data-stu-id="a0f4d-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="a0f4d-109">Cookie以 TempData 提供者為基礎</span><span class="sxs-lookup"><span data-stu-id="a0f4d-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="a0f4d-110">Azure App Service 記錄提供者</span><span class="sxs-lookup"><span data-stu-id="a0f4d-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="a0f4d-111">Azure Key Vault 設定提供者</span><span class="sxs-lookup"><span data-stu-id="a0f4d-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="a0f4d-112">Azure 與 Redis 儲存體資料保護金鑰存放庫</span><span class="sxs-lookup"><span data-stu-id="a0f4d-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="a0f4d-113">適用於 Windows 的 WebListener 伺服器</span><span class="sxs-lookup"><span data-stu-id="a0f4d-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="a0f4d-114">WebSocket 支援</span><span class="sxs-lookup"><span data-stu-id="a0f4d-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="a0f4d-115">在 1.0 版和 1.1 版的 ASP.NET Core 之間進行選擇</span><span class="sxs-lookup"><span data-stu-id="a0f4d-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="a0f4d-116">ASP.NET Core 1.1 的功能比 ASP.NET Core 1.0 更多。</span><span class="sxs-lookup"><span data-stu-id="a0f4d-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="a0f4d-117">一般情況下，建議您使用最新的版本。</span><span class="sxs-lookup"><span data-stu-id="a0f4d-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="a0f4d-118">其他資訊</span><span class="sxs-lookup"><span data-stu-id="a0f4d-118">Additional Information</span></span>

- [<span data-ttu-id="a0f4d-119">ASP.NET Core 1.1.0 版本資訊</span><span class="sxs-lookup"><span data-stu-id="a0f4d-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="a0f4d-120">若要了解 ASP.NET Core 開發小組的進度和計劃，請收聽 [ASP.NET Community Standup](https://live.asp.net/) (ASP.NET 社群之聲)。</span><span class="sxs-lookup"><span data-stu-id="a0f4d-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
