---
title: ASP.NET Core 安全性概觀
author: rick-anderson
description: 了解 ASP.NET Core 的驗證、授權和安全性基本概念。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051625"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="422b4-103">ASP.NET Core 安全性概觀</span><span class="sxs-lookup"><span data-stu-id="422b4-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="422b4-104">ASP.NET Core 可讓開發人員輕鬆設定應用程式的安全性並進行管理。</span><span class="sxs-lookup"><span data-stu-id="422b4-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="422b4-105">ASP.NET Core 包含管理驗證、授權、資料保護、HTTPS 強制、應用程式秘密、XSRF/CSRF 防護和 CORS 管理的功能。</span><span class="sxs-lookup"><span data-stu-id="422b4-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, XSRF/CSRF prevention, and CORS management.</span></span> <span data-ttu-id="422b4-106">這些安全性功能可讓您打造強固又安全的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="422b4-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="422b4-107">ASP.NET Core 安全性功能</span><span class="sxs-lookup"><span data-stu-id="422b4-107">ASP.NET Core security features</span></span>

<span data-ttu-id="422b4-108">ASP.NET Core 提供許多工具和程式庫來保護您的應用程式（包括內建的身分識別提供者），但您可以使用協力廠商身分識別服務，例如 Facebook、Twitter 和 LinkedIn。</span><span class="sxs-lookup"><span data-stu-id="422b4-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="422b4-109">使用 ASP.NET Core 時，您可以輕鬆管理應用程式密碼，以便使用機密資訊而不在程式碼中公開這些資訊。</span><span class="sxs-lookup"><span data-stu-id="422b4-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="422b4-110">驗證與授權</span><span class="sxs-lookup"><span data-stu-id="422b4-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="422b4-111">驗證是一道程序，其會將使用者提供的認證與作業系統、資料庫、應用程式或資源中儲存的認證進行比對。</span><span class="sxs-lookup"><span data-stu-id="422b4-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="422b4-112">如果相符的話，使用者就能成功通過驗證，並可執行他們在授權程序期間取得授權的動作。</span><span class="sxs-lookup"><span data-stu-id="422b4-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="422b4-113">授權則是決定使用者得以執行哪些動作的程序。</span><span class="sxs-lookup"><span data-stu-id="422b4-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="422b4-114">您也可以將驗證想成是進入伺服器、資料庫、應用程式或資源這類空間的方法，而授權則表示使用者得以針對空間 (伺服器、資料庫或應用程式) 內哪些物件執行哪些動作。</span><span class="sxs-lookup"><span data-stu-id="422b4-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="422b4-115">軟體的常見弱點</span><span class="sxs-lookup"><span data-stu-id="422b4-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="422b4-116">ASP.NET Core 和 EF 包含的功能有助您保護應用程式的安全，並防範安全性缺口的發生。</span><span class="sxs-lookup"><span data-stu-id="422b4-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="422b4-117">下列連結清單可帶您前往如何避免 Web 應用程式中最常見資訊安全漏洞的技術詳細文件：</span><span class="sxs-lookup"><span data-stu-id="422b4-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="422b4-118">跨網站腳本 (XSS) 攻擊</span><span class="sxs-lookup"><span data-stu-id="422b4-118">Cross-Site Scripting (XSS) attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="422b4-119">SQL 插入式攻擊</span><span class="sxs-lookup"><span data-stu-id="422b4-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="422b4-120">跨網站要求偽造 (XSRF/CSRF) 攻擊</span><span class="sxs-lookup"><span data-stu-id="422b4-120">Cross-Site Request Forgery (XSRF/CSRF) attacks</span></span>](xref:security/anti-request-forgery)
* <span data-ttu-id="422b4-121">[Open redirect attacks](xref:security/preventing-open-redirects) (開啟重新導向攻擊)</span><span class="sxs-lookup"><span data-stu-id="422b4-121">[Open redirect attacks](xref:security/preventing-open-redirects)</span></span>

<span data-ttu-id="422b4-122">除此之外，還有許多弱點是您必須提防的。</span><span class="sxs-lookup"><span data-stu-id="422b4-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="422b4-123">如需詳細資訊，請參閱目錄之 **安全性和 :::no-loc(Identity):::** 章節中的其他文章。</span><span class="sxs-lookup"><span data-stu-id="422b4-123">For more information, see the other articles in the **Security and :::no-loc(Identity):::** section of the table of contents.</span></span>
