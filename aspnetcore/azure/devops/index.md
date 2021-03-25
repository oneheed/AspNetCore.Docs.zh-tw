---
title: ASP.NET Core 與 Azure 的 DevOps
author: CamSoper
description: 本指南為如何為 Azure 上裝載的 ASP.NET Core 應用程式，建置 DevOps 管線的完整指導。
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: c49ecde1ab93f37a80ff604c7be00fa309d55ea7
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105050954"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="ccfec-103">ASP.NET Core 與 Azure 的 DevOps</span><span class="sxs-lookup"><span data-stu-id="ccfec-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="ccfec-104">[![封面影像](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="ccfec-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="ccfec-105">[Cam Soper](https://twitter.com/camsoper) 和 [Scott Addie](https://twitter.com/scottaddie) 著</span><span class="sxs-lookup"><span data-stu-id="ccfec-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="ccfec-106">本指南提供[可下載的 PDF 電子書](https://github.com/dotnet-architecture/eBooks/blob/master/current/devops-aspnet-core/DevOps-with-ASP.NET-Core-and-Azure.pdf)。</span><span class="sxs-lookup"><span data-stu-id="ccfec-106">This guide is available as a [downloadable PDF e-book](https://github.com/dotnet-architecture/eBooks/blob/master/current/devops-aspnet-core/DevOps-with-ASP.NET-Core-and-Azure.pdf).</span></span>

## <a name="welcome"></a><span data-ttu-id="ccfec-107">歡迎使用</span><span class="sxs-lookup"><span data-stu-id="ccfec-107">Welcome</span></span> 

<span data-ttu-id="ccfec-108">歡迎使用適用於.NET 的 Azure 開發生命週期指南！</span><span class="sxs-lookup"><span data-stu-id="ccfec-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="ccfec-109">本指南介紹如何使用.NET 工具及程序，為 Azure 建置開發生命週期的基本概念。</span><span class="sxs-lookup"><span data-stu-id="ccfec-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="ccfec-110">完成本指南之後，您就能充分運用完善 DevOps 工具鏈的各項優點。</span><span class="sxs-lookup"><span data-stu-id="ccfec-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="ccfec-111">本指南的適用對象</span><span class="sxs-lookup"><span data-stu-id="ccfec-111">Who this guide is for</span></span>

<span data-ttu-id="ccfec-112">您應是有經驗的 ASP.NET Core 開發人員 (200-300 級)。</span><span class="sxs-lookup"><span data-stu-id="ccfec-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="ccfec-113">因為本指南涵蓋 Azure 介紹，所以您無須具備這方面的知識。</span><span class="sxs-lookup"><span data-stu-id="ccfec-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="ccfec-114">本指南也適用於工作內容偏重於操作，而不在開發的 DevOps 工程師。</span><span class="sxs-lookup"><span data-stu-id="ccfec-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="ccfec-115">本指南的對象是 Windows 開發人員，</span><span class="sxs-lookup"><span data-stu-id="ccfec-115">This guide targets Windows developers.</span></span> <span data-ttu-id="ccfec-116">但 .NET Core 也 100% 支援 Linux 與 macOS。</span><span class="sxs-lookup"><span data-stu-id="ccfec-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="ccfec-117">若要將本指南應用到 Linux/macOS，請留意圖說文字所示的 Linux/macOS 差異。</span><span class="sxs-lookup"><span data-stu-id="ccfec-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="ccfec-118">本指南未說明的內容</span><span class="sxs-lookup"><span data-stu-id="ccfec-118">What this guide doesn't cover</span></span>

<span data-ttu-id="ccfec-119">本指南適用於.NET 開發人員，著重於端對端的持續部署體驗，</span><span class="sxs-lookup"><span data-stu-id="ccfec-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="ccfec-120">而不會詳盡解說 Azure 的一切，也不會在 Azure 服務適用的 .NET API 上著墨太多。</span><span class="sxs-lookup"><span data-stu-id="ccfec-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="ccfec-121">其會將重點圍繞在持續整合、部署、監視及偵錯上。</span><span class="sxs-lookup"><span data-stu-id="ccfec-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="ccfec-122">在快速入門的最後，則會提供後續步驟的建議。</span><span class="sxs-lookup"><span data-stu-id="ccfec-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="ccfec-123">建議內含對 ASP.NET Core 開發人員來說很實用的 Azure 平台服務。</span><span class="sxs-lookup"><span data-stu-id="ccfec-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="ccfec-124">本指南內容</span><span class="sxs-lookup"><span data-stu-id="ccfec-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="ccfec-125">工具及下載</span><span class="sxs-lookup"><span data-stu-id="ccfec-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="ccfec-126">了解如何取得本指南中使用的工具。</span><span class="sxs-lookup"><span data-stu-id="ccfec-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="ccfec-127">部署到 App Service</span><span class="sxs-lookup"><span data-stu-id="ccfec-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="ccfec-128">了解各種如何將 ASP.NET Core 應用程式部署到 Azure App Service 的方法。</span><span class="sxs-lookup"><span data-stu-id="ccfec-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="ccfec-129">持續整合與部署</span><span class="sxs-lookup"><span data-stu-id="ccfec-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="ccfec-130">使用 GitHub、Azure DevOps Services 與 Azure，為您的 ASP.NET Core 應用程式建置端對端的持續整合與部署解決方案。</span><span class="sxs-lookup"><span data-stu-id="ccfec-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="ccfec-131">監視和調試</span><span class="sxs-lookup"><span data-stu-id="ccfec-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="ccfec-132">使用 Azure 工來監視、疑難排解問題，以及微調您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="ccfec-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="ccfec-133">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ccfec-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="ccfec-134">ASP.NET Core 開發人員學習 Azure 的其他學習途徑。</span><span class="sxs-lookup"><span data-stu-id="ccfec-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="ccfec-135">其他入門閱讀資料</span><span class="sxs-lookup"><span data-stu-id="ccfec-135">Additional introductory reading</span></span>

<span data-ttu-id="ccfec-136">如果這是您第一次接觸雲端運算，這些文章會說明基本概念。</span><span class="sxs-lookup"><span data-stu-id="ccfec-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="ccfec-137">什麼是雲端運算？</span><span class="sxs-lookup"><span data-stu-id="ccfec-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="ccfec-138">雲端運算的範例</span><span class="sxs-lookup"><span data-stu-id="ccfec-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="ccfec-139">什麼是 IaaS？</span><span class="sxs-lookup"><span data-stu-id="ccfec-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="ccfec-140">什麼是 PaaS？</span><span class="sxs-lookup"><span data-stu-id="ccfec-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
