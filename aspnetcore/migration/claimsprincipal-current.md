---
title: 從 ClaimsPrincipal 遷移
author: mjrousos
description: 瞭解如何從 ClaimsPrincipal 遷移，以在 ASP.NET Core 中取得目前已驗證的使用者身分識別和宣告。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 3aa0adb299789efbb071cdb934d43832a84cf540
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059763"
---
# <a name="migrate-from-claimsprincipalcurrent"></a><span data-ttu-id="e6fdd-103">從 ClaimsPrincipal 遷移</span><span class="sxs-lookup"><span data-stu-id="e6fdd-103">Migrate from ClaimsPrincipal.Current</span></span>

<span data-ttu-id="e6fdd-104">在 ASP.NET 4.x 專案中，通常會使用 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current) 來取得目前已驗證使用者的身分識別和宣告。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-104">In ASP.NET 4.x projects, it was common to use [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) to retrieve the current authenticated user's identity and claims.</span></span> <span data-ttu-id="e6fdd-105">在 ASP.NET Core 中，不再設定這個屬性。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-105">In ASP.NET Core, this property is no longer set.</span></span> <span data-ttu-id="e6fdd-106">根據此程式碼需要更新，以透過不同的方式取得目前已驗證的使用者身分識別。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-106">Code that was depending on it needs to be updated to get the current authenticated user's identity through a different means.</span></span>

## <a name="context-specific-state-instead-of-static-state"></a><span data-ttu-id="e6fdd-107">特定內容狀態，而不是靜態狀態</span><span class="sxs-lookup"><span data-stu-id="e6fdd-107">Context-specific state instead of static state</span></span>

<span data-ttu-id="e6fdd-108">使用 ASP.NET Core 時，和的值都 `ClaimsPrincipal.Current` `Thread.CurrentPrincipal` 不會設定。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-108">When using ASP.NET Core, the values of both `ClaimsPrincipal.Current` and `Thread.CurrentPrincipal` aren't set.</span></span> <span data-ttu-id="e6fdd-109">這些屬性都代表靜態狀態，ASP.NET Core 通常會避免。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-109">These properties both represent static state, which ASP.NET Core generally avoids.</span></span> <span data-ttu-id="e6fdd-110">相反地，ASP.NET Core 會使用相依性 [插入](xref:fundamentals/dependency-injection) (DI) 來提供相依性，例如目前使用者的身分識別。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-110">Instead, ASP.NET Core uses [dependency injection](xref:fundamentals/dependency-injection) (DI) to provide dependencies such as the current user's identity.</span></span> <span data-ttu-id="e6fdd-111">從 DI 取得目前使用者的身分識別也會更容易測試，因為可以輕鬆地插入測試身分識別。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-111">Getting the current user's identity from DI is more testable, too, since test identities can be easily injected.</span></span>

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a><span data-ttu-id="e6fdd-112">在 ASP.NET Core 應用程式中取出目前的使用者</span><span class="sxs-lookup"><span data-stu-id="e6fdd-112">Retrieve the current user in an ASP.NET Core app</span></span>

<span data-ttu-id="e6fdd-113">有幾個選項可以用來在 ASP.NET Core 中抓取目前已驗證的使用者， `ClaimsPrincipal` 以取代 `ClaimsPrincipal.Current` ：</span><span class="sxs-lookup"><span data-stu-id="e6fdd-113">There are several options for retrieving the current authenticated user's `ClaimsPrincipal` in ASP.NET Core in place of `ClaimsPrincipal.Current`:</span></span>

* <span data-ttu-id="e6fdd-114">**ControllerBase。**</span><span class="sxs-lookup"><span data-stu-id="e6fdd-114">**ControllerBase.User**.</span></span> <span data-ttu-id="e6fdd-115">MVC 控制器可以使用其 [使用者](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) 屬性來存取目前已驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-115">MVC controllers can access the current authenticated user with their [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) property.</span></span>
* <span data-ttu-id="e6fdd-116">**HttpCoNtext. 使用者** 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-116">**HttpContext.User**.</span></span> <span data-ttu-id="e6fdd-117">可存取目前 `HttpContext` (中介軟體的元件，例如) 可以從 HttpCoNtext 取得目前的使用者 `ClaimsPrincipal` 。 [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)</span><span class="sxs-lookup"><span data-stu-id="e6fdd-117">Components with access to the current `HttpContext` (middleware, for example) can get the current user's `ClaimsPrincipal` from [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).</span></span>
* <span data-ttu-id="e6fdd-118">**從呼叫端傳入** 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-118">**Passed in from caller**.</span></span> <span data-ttu-id="e6fdd-119">沒有目前存取權的程式庫 `HttpContext` 通常會從控制器或中介軟體元件呼叫，並且可以將目前使用者的身分識別做為引數傳遞。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-119">Libraries without access to the current `HttpContext` are often called from controllers or middleware components and can have the current user's identity passed as an argument.</span></span>
* <span data-ttu-id="e6fdd-120">**>iHTTPcoNtextaccessor** 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-120">**IHttpContextAccessor**.</span></span> <span data-ttu-id="e6fdd-121">正在遷移至 ASP.NET Core 的專案可能太大，無法輕鬆地將目前使用者的身分識別傳遞至所有必要的位置。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-121">The project being migrated to ASP.NET Core may be too large to easily pass the current user's identity to all necessary locations.</span></span> <span data-ttu-id="e6fdd-122">在這種情況下， [>iHTTPcoNtextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) 可用來作為因應措施。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-122">In such cases, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) can be used as a workaround.</span></span> <span data-ttu-id="e6fdd-123">`IHttpContextAccessor` 可以存取目前的 (（ `HttpContext` 如果有的話）) 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-123">`IHttpContextAccessor` is able to access the current `HttpContext` (if one exists).</span></span> <span data-ttu-id="e6fdd-124">如果正在使用 DI，請參閱 <xref:fundamentals/httpcontext> 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-124">If DI is being used, see <xref:fundamentals/httpcontext>.</span></span> <span data-ttu-id="e6fdd-125">在程式碼中取得目前使用者的身分識別，但尚未更新為使用 ASP.NET Core 的 DI 驅動架構的短期解決方案如下：</span><span class="sxs-lookup"><span data-stu-id="e6fdd-125">A short-term solution to getting the current user's identity in code that hasn't yet been updated to work with ASP.NET Core's DI-driven architecture would be:</span></span>

  * <span data-ttu-id="e6fdd-126">在 `IHttpContextAccessor` 中呼叫 [AddHttpCoNtextAccessor](https://github.com/aspnet/Hosting/issues/793) ，以便在 DI 容器中使用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-126">Make `IHttpContextAccessor` available in the DI container by calling [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`.</span></span>
  * <span data-ttu-id="e6fdd-127">在啟動期間取得的實例 `IHttpContextAccessor` ，並將它儲存在靜態變數中。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-127">Get an instance of `IHttpContextAccessor` during startup and store it in a static variable.</span></span> <span data-ttu-id="e6fdd-128">此實例可供先前從靜態屬性取得目前使用者的程式碼使用。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-128">The instance is made available to code that was previously retrieving the current user from a static property.</span></span>
  * <span data-ttu-id="e6fdd-129">使用取得目前使用者的 `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User` 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-129">Retrieve the current user's `ClaimsPrincipal` using `HttpContextAccessor.HttpContext?.User`.</span></span> <span data-ttu-id="e6fdd-130">如果在 HTTP 要求的內容之外使用此程式碼，則 `HttpContext` 為 null。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-130">If this code is used outside of the context of an HTTP request, the `HttpContext` is null.</span></span>

<span data-ttu-id="e6fdd-131">最後一個選項（使用 `IHttpContextAccessor` 儲存在靜態變數中的實例），與將插入的相依性插入靜態相依性的 ASP.NET Core 準則相反。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-131">The final option, using an `IHttpContextAccessor` instance stored in a static variable, is contrary to the ASP.NET Core principle of preferring injected dependencies to static dependencies.</span></span> <span data-ttu-id="e6fdd-132">規劃最後改為 `IHttpContextAccessor` 從 DI 取出實例。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-132">Plan to eventually retrieve `IHttpContextAccessor` instances from DI instead.</span></span> <span data-ttu-id="e6fdd-133">但是，當您遷移使用的大型現有 ASP.NET 應用程式時，靜態協助程式可能是很有用的橋樑 `ClaimsPrincipal.Current` 。</span><span class="sxs-lookup"><span data-stu-id="e6fdd-133">A static helper can be a useful bridge, though, when migrating large existing ASP.NET apps that use `ClaimsPrincipal.Current`.</span></span>
