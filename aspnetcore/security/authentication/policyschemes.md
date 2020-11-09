---
title: ASP.NET Core 中的原則架構
author: rick-anderson
description: 驗證原則配置可讓您更輕鬆地擁有單一邏輯驗證配置
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053224"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="aff12-103">ASP.NET Core 中的原則架構</span><span class="sxs-lookup"><span data-stu-id="aff12-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="aff12-104">驗證原則配置可讓單一邏輯驗證配置更容易使用多種方法。</span><span class="sxs-lookup"><span data-stu-id="aff12-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="aff12-105">例如，原則配置可能會針對挑戰使用 Google 驗證，並針對 cookie 其他所有專案使用驗證。</span><span class="sxs-lookup"><span data-stu-id="aff12-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="aff12-106">驗證原則配置可讓它：</span><span class="sxs-lookup"><span data-stu-id="aff12-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="aff12-107">輕鬆將任何驗證動作轉寄至另一個配置。</span><span class="sxs-lookup"><span data-stu-id="aff12-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="aff12-108">根據要求進行動態轉寄。</span><span class="sxs-lookup"><span data-stu-id="aff12-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="aff12-109">使用衍生的所有驗證配置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> 以及相關聯[的 \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)：</span><span class="sxs-lookup"><span data-stu-id="aff12-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="aff12-110">會在 ASP.NET Core 2.1 和更新版本中自動設定原則。</span><span class="sxs-lookup"><span data-stu-id="aff12-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="aff12-111">可以透過設定配置的選項來啟用。</span><span class="sxs-lookup"><span data-stu-id="aff12-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="aff12-112">範例</span><span class="sxs-lookup"><span data-stu-id="aff12-112">Examples</span></span>

<span data-ttu-id="aff12-113">下列範例會顯示結合較低層級配置的較高層級配置。</span><span class="sxs-lookup"><span data-stu-id="aff12-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="aff12-114">Google 驗證會用於挑戰，而 cookie 驗證則用於其他所有專案：</span><span class="sxs-lookup"><span data-stu-id="aff12-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="aff12-115">下列範例會針對每個要求啟用動態選取的配置。</span><span class="sxs-lookup"><span data-stu-id="aff12-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="aff12-116">也就是說，如何混合 cookie 和 API 驗證：</span><span class="sxs-lookup"><span data-stu-id="aff12-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
