---
title: 適用于 ASP.NET Core 的驗證範例
author: rick-anderson
description: 提供 ASP.NET Core 存放庫中驗證範例的連結。
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110114"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="b2d01-103">適用于 ASP.NET Core 的驗證範例</span><span class="sxs-lookup"><span data-stu-id="b2d01-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="b2d01-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2d01-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b2d01-105">[ASP.NET Core 存放庫](https://github.com/dotnet/aspnetcore)包含下列 (分支) 的驗證範例 `main` ：</span><span class="sxs-lookup"><span data-stu-id="b2d01-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="b2d01-106">宣告轉換</span><span class="sxs-lookup"><span data-stu-id="b2d01-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="b2d01-107">[Cookie 認證](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="b2d01-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="b2d01-108">自訂授權失敗回應</span><span class="sxs-lookup"><span data-stu-id="b2d01-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="b2d01-109">自訂原則提供者-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="b2d01-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="b2d01-110">動態驗證方案和選項</span><span class="sxs-lookup"><span data-stu-id="b2d01-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="b2d01-111">[外部宣告](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="b2d01-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="b2d01-112">cookie根據要求選取和其他驗證配置</span><span class="sxs-lookup"><span data-stu-id="b2d01-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="b2d01-113">限制存取靜態檔案</span><span class="sxs-lookup"><span data-stu-id="b2d01-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="b2d01-114">取得並執行範例</span><span class="sxs-lookup"><span data-stu-id="b2d01-114">Obtain and run the samples</span></span>

<span data-ttu-id="b2d01-115">本文提供的範例連結提供即將發行的 ASP.NET Core 範例。</span><span class="sxs-lookup"><span data-stu-id="b2d01-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="b2d01-116">若要取得目前版本或先前版本的範例，請執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="b2d01-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="b2d01-117">選取 [ASP.NET Core 存放庫](https://github.com/dotnet/aspnetcore)的發行分支] (https://github.com/dotnet/aspnetcore) 。</span><span class="sxs-lookup"><span data-stu-id="b2d01-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="b2d01-118">例如， `release/5.0` 分支包含 ASP.NET Core 5.0 版本的範例。</span><span class="sxs-lookup"><span data-stu-id="b2d01-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="b2d01-119">複製或下載 ASP.NET Core 存放庫。</span><span class="sxs-lookup"><span data-stu-id="b2d01-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="b2d01-120">在您的本機系統上，確認已安裝符合 ASP.NET Core 存放庫複製的 [.Net CORE SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。</span><span class="sxs-lookup"><span data-stu-id="b2d01-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="b2d01-121">流覽至資料夾中的範例 `aspnetcore/src/Security/samples` ，並使用[ `dotnet run` 命令](/dotnet/core/tools/dotnet-run)執行範例。</span><span class="sxs-lookup"><span data-stu-id="b2d01-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>
