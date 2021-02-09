---
title: 設定 ASP.NET Core 的修剪器 Blazor
author: guardrex
description: 瞭解如何在建立應用程式時，控制 (IL) 連結器 (修剪器) 的中繼語言 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975207"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="2a0c1-103">設定 ASP.NET Core 的修剪器 Blazor</span><span class="sxs-lookup"><span data-stu-id="2a0c1-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2a0c1-104">Blazor WebAssembly 執行 [ (IL) 修剪的中繼語言 ](/dotnet/standard/managed-code#intermediate-language--execution) ，以縮減已發行輸出的大小。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="2a0c1-105">依預設，會在發佈應用程式時進行修剪。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="2a0c1-106">修剪可能會產生不利的影響。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="2a0c1-107">在使用反映的應用程式中，修剪器通常無法判斷執行時間所需的反映類型。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="2a0c1-108">若要修剪使用反映的應用程式，必須在應用程式的程式碼和應用程式所相依的封裝或架構中，針對反映的必要類型通知修剪器。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="2a0c1-109">修剪器在執行時間也無法回應應用程式的動態行為。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="2a0c1-110">若要確保已修剪的應用程式在部署後可以正常運作，請在開發期間經常測試已發佈的輸出。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="2a0c1-111">若要設定修剪器，請參閱 .NET 基本概念檔中的 [修剪選項](/dotnet/core/deploying/trimming-options) 一文，其中包含下列主題的指引：</span><span class="sxs-lookup"><span data-stu-id="2a0c1-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="2a0c1-112">使用專案檔中的屬性來停用整個應用程式的修剪 `<PublishTrimmed>` 。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="2a0c1-113">控制修剪器捨棄未使用 IL 的程度。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="2a0c1-114">停止修剪器，使其無法修剪特定的元件。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="2a0c1-115">用於修剪的「根」元件。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="2a0c1-116">藉由在專案檔中將屬性設為，以呈現反映類型的警告 `<SuppressTrimAnalysisWarnings>` `false` 。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="2a0c1-117">控制符號修剪和 degugger 支援。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="2a0c1-118">設定修剪架構程式庫功能的修剪器功能。</span><span class="sxs-lookup"><span data-stu-id="2a0c1-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a0c1-119">其他資源</span><span class="sxs-lookup"><span data-stu-id="2a0c1-119">Additional resources</span></span>

* [<span data-ttu-id="2a0c1-120">修剪獨立式部署及可執行檔</span><span class="sxs-lookup"><span data-stu-id="2a0c1-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
