---
title: 後續步驟-使用 ASP.NET Core 和 Azure DevOps
author: CamSoper
description: 使用 ASP.NET Core 和 Azure 的其他 DevOps 學習路徑。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056591"
---
# <a name="next-steps"></a>後續步驟

在本指南中，您已為 ASP.NET Core 範例應用程式建立 DevOps 管線。 恭喜！ 我們希望您喜歡學習將 ASP.NET Core web 應用程式發佈至 Azure App Service，並自動化變更的持續整合。

除了 web 裝載和 DevOps 之外，Azure 也有各式各樣的平臺即服務 (PaaS) 服務，對 ASP.NET Core 開發人員非常有用。 本節簡要說明一些最常使用的服務。

## <a name="storage-and-databases"></a>儲存體和資料庫

[Redis](/azure/redis-cache/) 快取是以服務形式提供的高輸送量、低延遲資料快取。 它可用於快取頁面輸出、減少資料庫要求，以及在應用程式的多個實例之間提供 ASP.NET Core 的會話狀態。

[Azure 儲存體](/azure/storage/) 是 Azure 可大規模調整的雲端儲存體。 開發人員可以利用 [佇列儲存體](/azure/storage/queues/storage-queues-introduction) 進行可靠的訊息佇列，而 [資料表儲存體](/azure/storage/tables/table-storage-overview) 是 NoSQL 索引鍵/值存放區，專為使用大量、半結構化資料集的快速開發所設計。

[Azure SQL Database](/azure/sql-database/) 使用 Microsoft SQL Server 引擎提供熟悉的關係資料庫功能做為服務。

[Cosmos DB](/azure/cosmos-db/) 全域散發、多模型的 NoSQL 資料庫服務。 有多個 Api 可供使用，包括先前稱為 DocumentDB) 、Cassandra 和 MongoDB 的 SQL API (。

## Identity

[Azure Active Directory](/azure/active-directory/) 和 [Azure Active Directory B2C](/azure/active-directory-b2c/) 都是身分識別服務。 Azure Active Directory 是專為企業案例而設計，可讓 Azure AD B2B (企業對企業) 共同作業，同時 Azure Active Directory B2C 是預期的企業對客戶案例，包括社交網路登入。

## <a name="mobile"></a>行動

[通知中樞](/azure/notification-hubs/) 是多平臺、可調整的推播通知引擎，可快速地將數百萬則訊息傳送到在各種類型的裝置上執行的應用程式。

## <a name="web-infrastructure"></a>Web 基礎結構

[Azure Container Service](/azure/aks/) 管理您的託管 Kubernetes 環境，讓您快速且輕鬆地部署和管理容器化應用程式，而不需要容器協調流程專業知識。

[Azure 搜尋](/azure/search/) 服務是用來建立超過私人、非廣泛內容的企業搜尋解決方案。

[Service Fabric](/azure/service-fabric/) 是一種分散式系統平臺，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務與容器。
