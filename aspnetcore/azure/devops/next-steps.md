---
title: 後續步驟-使用 ASP.NET Core 和 Azure DevOps
author: CamSoper
description: 使用 ASP.NET Core 和 Azure 的其他 DevOps 學習路徑。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056591"
---
# <a name="next-steps"></a><span data-ttu-id="0d332-103">後續步驟</span><span class="sxs-lookup"><span data-stu-id="0d332-103">Next steps</span></span>

<span data-ttu-id="0d332-104">在本指南中，您已為 ASP.NET Core 範例應用程式建立 DevOps 管線。</span><span class="sxs-lookup"><span data-stu-id="0d332-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="0d332-105">恭喜！</span><span class="sxs-lookup"><span data-stu-id="0d332-105">Congratulations!</span></span> <span data-ttu-id="0d332-106">我們希望您喜歡學習將 ASP.NET Core web 應用程式發佈至 Azure App Service，並自動化變更的持續整合。</span><span class="sxs-lookup"><span data-stu-id="0d332-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="0d332-107">除了 web 裝載和 DevOps 之外，Azure 也有各式各樣的平臺即服務 (PaaS) 服務，對 ASP.NET Core 開發人員非常有用。</span><span class="sxs-lookup"><span data-stu-id="0d332-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="0d332-108">本節簡要說明一些最常使用的服務。</span><span class="sxs-lookup"><span data-stu-id="0d332-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="0d332-109">儲存體和資料庫</span><span class="sxs-lookup"><span data-stu-id="0d332-109">Storage and databases</span></span>

<span data-ttu-id="0d332-110">[Redis](/azure/redis-cache/) 快取是以服務形式提供的高輸送量、低延遲資料快取。</span><span class="sxs-lookup"><span data-stu-id="0d332-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="0d332-111">它可用於快取頁面輸出、減少資料庫要求，以及在應用程式的多個實例之間提供 ASP.NET Core 的會話狀態。</span><span class="sxs-lookup"><span data-stu-id="0d332-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="0d332-112">[Azure 儲存體](/azure/storage/) 是 Azure 可大規模調整的雲端儲存體。</span><span class="sxs-lookup"><span data-stu-id="0d332-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="0d332-113">開發人員可以利用 [佇列儲存體](/azure/storage/queues/storage-queues-introduction) 進行可靠的訊息佇列，而 [資料表儲存體](/azure/storage/tables/table-storage-overview) 是 NoSQL 索引鍵/值存放區，專為使用大量、半結構化資料集的快速開發所設計。</span><span class="sxs-lookup"><span data-stu-id="0d332-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="0d332-114">[Azure SQL Database](/azure/sql-database/) 使用 Microsoft SQL Server 引擎提供熟悉的關係資料庫功能做為服務。</span><span class="sxs-lookup"><span data-stu-id="0d332-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="0d332-115">[Cosmos DB](/azure/cosmos-db/) 全域散發、多模型的 NoSQL 資料庫服務。</span><span class="sxs-lookup"><span data-stu-id="0d332-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="0d332-116">有多個 Api 可供使用，包括先前稱為 DocumentDB) 、Cassandra 和 MongoDB 的 SQL API (。</span><span class="sxs-lookup"><span data-stu-id="0d332-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="0d332-117">[Azure Active Directory](/azure/active-directory/) 和 [Azure Active Directory B2C](/azure/active-directory-b2c/) 都是身分識別服務。</span><span class="sxs-lookup"><span data-stu-id="0d332-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="0d332-118">Azure Active Directory 是專為企業案例而設計，可讓 Azure AD B2B (企業對企業) 共同作業，同時 Azure Active Directory B2C 是預期的企業對客戶案例，包括社交網路登入。</span><span class="sxs-lookup"><span data-stu-id="0d332-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="0d332-119">行動</span><span class="sxs-lookup"><span data-stu-id="0d332-119">Mobile</span></span>

<span data-ttu-id="0d332-120">[通知中樞](/azure/notification-hubs/) 是多平臺、可調整的推播通知引擎，可快速地將數百萬則訊息傳送到在各種類型的裝置上執行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0d332-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="0d332-121">Web 基礎結構</span><span class="sxs-lookup"><span data-stu-id="0d332-121">Web infrastructure</span></span>

<span data-ttu-id="0d332-122">[Azure Container Service](/azure/aks/) 管理您的託管 Kubernetes 環境，讓您快速且輕鬆地部署和管理容器化應用程式，而不需要容器協調流程專業知識。</span><span class="sxs-lookup"><span data-stu-id="0d332-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="0d332-123">[Azure 搜尋](/azure/search/) 服務是用來建立超過私人、非廣泛內容的企業搜尋解決方案。</span><span class="sxs-lookup"><span data-stu-id="0d332-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="0d332-124">[Service Fabric](/azure/service-fabric/) 是一種分散式系統平臺，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務與容器。</span><span class="sxs-lookup"><span data-stu-id="0d332-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
