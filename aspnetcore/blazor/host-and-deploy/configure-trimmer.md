---
title: 設定 ASP.NET Core 的修剪器 Blazor
author: guardrex
description: 瞭解如何在建立應用程式時，控制 (IL) 連結器 (修剪器) 的中繼語言 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 2923f76c586465e4e6044763f18527a7d36ad57c
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080861"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="1522c-103">設定 ASP.NET Core 的修剪器 Blazor</span><span class="sxs-lookup"><span data-stu-id="1522c-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="1522c-104">依 [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="1522c-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="1522c-105">Blazor WebAssembly 執行 [ (IL) 修剪的中繼語言 ](/dotnet/standard/managed-code#intermediate-language--execution) ，以縮減已發行輸出的大小。</span><span class="sxs-lookup"><span data-stu-id="1522c-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="1522c-106">調整應用程式的大小會優化，但可能會產生不利的影響。</span><span class="sxs-lookup"><span data-stu-id="1522c-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="1522c-107">使用反映或相關動態功能的應用程式可能會在修剪時中斷，因為修剪器不知道動態行為，而且無法判斷在執行時間的反映需要哪些類型。</span><span class="sxs-lookup"><span data-stu-id="1522c-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="1522c-108">若要修剪這類應用程式，必須在程式碼中和應用程式相依的封裝或架構中，通知修剪器所需的任何類型。</span><span class="sxs-lookup"><span data-stu-id="1522c-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="1522c-109">若要確保已修剪的應用程式在部署後可以正常運作，請務必在開發期間經常測試已發佈的輸出。</span><span class="sxs-lookup"><span data-stu-id="1522c-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="1522c-110">您可以 `PublishTrimmed` `false` 在應用程式的專案檔中，將 MSBuild 屬性設定為，以停用 .net 應用程式的修剪：</span><span class="sxs-lookup"><span data-stu-id="1522c-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="1522c-111">其他資源</span><span class="sxs-lookup"><span data-stu-id="1522c-111">Additional resources</span></span>

* [<span data-ttu-id="1522c-112">修剪獨立式部署及可執行檔</span><span class="sxs-lookup"><span data-stu-id="1522c-112">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
