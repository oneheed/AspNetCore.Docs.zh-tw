---
title: ASP.NET Core 中的簡單授權
author: rick-anderson
description: 瞭解如何使用授權屬性來限制對 ASP.NET Core 控制器和動作的存取。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/simple
ms.openlocfilehash: edf027b23ba6b22146e4521c134e67ac6fe1bd03
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634796"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="28587-103">ASP.NET Core 中的簡單授權</span><span class="sxs-lookup"><span data-stu-id="28587-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="28587-104">ASP.NET Core 中的授權是由 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 和其各種參數所控制。</span><span class="sxs-lookup"><span data-stu-id="28587-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="28587-105">將 `[Authorize]` 屬性套用至控制器、動作或頁面最簡單的形式， Razor 會將該元件的存取限制為任何已驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="28587-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="28587-106">例如，下列程式碼會將存取許可權制 `AccountController` 為任何已驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="28587-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="28587-107">如果您想要將授權套用至動作，而不是控制站，請將 `AuthorizeAttribute` 屬性套用至動作本身：</span><span class="sxs-lookup"><span data-stu-id="28587-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="28587-108">現在只有經過驗證的使用者可以存取函式 `Logout` 。</span><span class="sxs-lookup"><span data-stu-id="28587-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="28587-109">您也可以使用 `AllowAnonymous` 屬性來允許未經驗證的使用者存取個別動作。</span><span class="sxs-lookup"><span data-stu-id="28587-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="28587-110">例如：</span><span class="sxs-lookup"><span data-stu-id="28587-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="28587-111">這只允許已驗證的使用者 `AccountController` ，除了 `Login` 可供所有人存取的動作之外，不論他們的驗證或未驗證/匿名狀態為何。</span><span class="sxs-lookup"><span data-stu-id="28587-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="28587-112">`[AllowAnonymous]` 略過所有授權語句。</span><span class="sxs-lookup"><span data-stu-id="28587-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="28587-113">如果您結合 `[AllowAnonymous]` 了和任何 `[Authorize]` 屬性， `[Authorize]` 就會忽略屬性。</span><span class="sxs-lookup"><span data-stu-id="28587-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="28587-114">例如，如果您套用於 `[AllowAnonymous]` 控制器層級，則 `[Authorize]` 會忽略相同控制器上的任何屬性 (或它) 內的任何動作。</span><span class="sxs-lookup"><span data-stu-id="28587-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a><span data-ttu-id="28587-115">授權屬性和 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="28587-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="28587-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>***無法***套用至 Razor 頁面處理常式。</span><span class="sxs-lookup"><span data-stu-id="28587-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="28587-117">例如， `[Authorize]` 無法套用至 `OnGet` 、 `OnPost` 或任何其他頁面處理常式。</span><span class="sxs-lookup"><span data-stu-id="28587-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="28587-118">針對不同的處理常式，請考慮針對具有不同授權需求的頁面使用 ASP.NET Core 的 MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="28587-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="28587-119">下列兩種方法可以用來將授權套用至 Razor 頁面處理常式方法：</span><span class="sxs-lookup"><span data-stu-id="28587-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="28587-120">針對需要不同授權的頁面處理常式，請使用不同的頁面。</span><span class="sxs-lookup"><span data-stu-id="28587-120">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="28587-121">已將共用內容移至一或多個 [部分的視圖](xref:mvc/views/partial)。</span><span class="sxs-lookup"><span data-stu-id="28587-121">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="28587-122">可能的話，這是建議的方法。</span><span class="sxs-lookup"><span data-stu-id="28587-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="28587-123">對於必須共用通用頁面的內容，請撰寫可執行授權做為 [>iasyncpagefilter OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)一部分的篩選準則。</span><span class="sxs-lookup"><span data-stu-id="28587-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="28587-124">[PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub 專案會示範這種方法：</span><span class="sxs-lookup"><span data-stu-id="28587-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="28587-125">[AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs)會實行授權篩選準則：[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="28587-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="28587-126">[[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16)屬性會套用至 `OnGet` 頁面處理常式：[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="28587-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="28587-127">[PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth)範例***方法不會：***</span><span class="sxs-lookup"><span data-stu-id="28587-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="28587-128">以套用至頁面、頁面模型或全域的授權屬性撰寫。</span><span class="sxs-lookup"><span data-stu-id="28587-128">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="28587-129">當您有一或多個 `AuthorizeAttribute` 實例也套用至頁面時，撰寫授權屬性會導致驗證和授權執行多次 `AuthorizeFilter` 。</span><span class="sxs-lookup"><span data-stu-id="28587-129">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="28587-130">搭配 ASP.NET Core authentication 與授權系統的其餘部分一起使用。</span><span class="sxs-lookup"><span data-stu-id="28587-130">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="28587-131">您必須確認應用程式的使用方式正確。</span><span class="sxs-lookup"><span data-stu-id="28587-131">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="28587-132">沒有計劃支援 `AuthorizeAttribute` on Razor 頁面處理常式。</span><span class="sxs-lookup"><span data-stu-id="28587-132">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
