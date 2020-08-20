---
title: ASP.NET Core 的驗證範例
author: rick-anderson
description: 提供 ASP.NET Core 存放庫中驗證範例的連結。
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630740"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="bb5eb-103">ASP.NET Core 的驗證範例</span><span class="sxs-lookup"><span data-stu-id="bb5eb-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="bb5eb-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb5eb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb5eb-105">[ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)會在*AspNetCore/src/Security/samples*資料夾中包含下列驗證範例：</span><span class="sxs-lookup"><span data-stu-id="bb5eb-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="bb5eb-106">宣告轉換</span><span class="sxs-lookup"><span data-stu-id="bb5eb-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="bb5eb-107">[Cookie 認證](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="bb5eb-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="bb5eb-108">自訂原則提供者-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="bb5eb-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="bb5eb-109">動態驗證方案和選項</span><span class="sxs-lookup"><span data-stu-id="bb5eb-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="bb5eb-110">[外部宣告](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="bb5eb-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="bb5eb-111">cookie根據要求選取和其他驗證配置</span><span class="sxs-lookup"><span data-stu-id="bb5eb-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="bb5eb-112">限制存取靜態檔案</span><span class="sxs-lookup"><span data-stu-id="bb5eb-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="bb5eb-113">執行範例</span><span class="sxs-lookup"><span data-stu-id="bb5eb-113">Run the samples</span></span>

* <span data-ttu-id="bb5eb-114">選取 [分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="bb5eb-115">例如， `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="bb5eb-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="bb5eb-116">複製或下載 [ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="bb5eb-117">確認您已安裝符合 ASP.NET Core 存放庫複製的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="bb5eb-118">流覽至 *AspNetCore/src/Security/* sample 中的範例，並使用執行範例 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb5eb-119">[ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)會在*AspNetCore/src/Security/samples*資料夾中包含下列驗證範例：</span><span class="sxs-lookup"><span data-stu-id="bb5eb-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="bb5eb-120">宣告轉換</span><span class="sxs-lookup"><span data-stu-id="bb5eb-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="bb5eb-121">[Cookie 認證](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="bb5eb-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="bb5eb-122">自訂原則提供者-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="bb5eb-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="bb5eb-123">動態驗證方案和選項</span><span class="sxs-lookup"><span data-stu-id="bb5eb-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="bb5eb-124">[外部宣告](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="bb5eb-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="bb5eb-125">cookie根據要求選取和其他驗證配置</span><span class="sxs-lookup"><span data-stu-id="bb5eb-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="bb5eb-126">限制存取靜態檔案</span><span class="sxs-lookup"><span data-stu-id="bb5eb-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="bb5eb-127">執行範例</span><span class="sxs-lookup"><span data-stu-id="bb5eb-127">Run the samples</span></span>

* <span data-ttu-id="bb5eb-128">選取 [分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="bb5eb-129">例如， `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="bb5eb-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="bb5eb-130">複製或下載 [ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="bb5eb-131">確認您已安裝符合 ASP.NET Core 存放庫複製的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="bb5eb-132">流覽至 *AspNetCore/src/Security/* sample 中的範例，並使用執行範例 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="bb5eb-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
