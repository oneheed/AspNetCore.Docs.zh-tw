---
title: ASP.NET Core 中的簡單授權
author: rick-anderson
description: 瞭解如何使用授權屬性來限制存取 ASP.NET 核心控制器和動作。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/simple
ms.openlocfilehash: eaf36822edc47f7238c356e167b05ac44f93175d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587563"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core 中的簡單授權

<a name="security-authorization-simple"></a>

ASP.NET Core 中的授權是由 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 和其各種參數所控制。 將 `[Authorize]` 屬性套用至控制器、動作或頁面最簡單的形式， Razor 會將該元件的存取限制為任何已驗證的使用者。

例如，下列程式碼會將存取許可權制 `AccountController` 為任何已驗證的使用者。

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

如果您想要將授權套用至動作，而不是控制站，請將 `AuthorizeAttribute` 屬性套用至動作本身：

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

現在只有經過驗證的使用者可以存取函式 `Logout` 。

您也可以使用 `AllowAnonymous` 屬性來允許未經驗證的使用者存取個別動作。 例如：

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

這只允許已驗證的使用者 `AccountController` ，除了 `Login` 可供所有人存取的動作之外，不論他們的驗證或未驗證/匿名狀態為何。

> [!WARNING]
> `[AllowAnonymous]` 略過所有授權語句。 如果您結合 `[AllowAnonymous]` 了和任何 `[Authorize]` 屬性， `[Authorize]` 就會忽略屬性。 例如，如果您套用於 `[AllowAnonymous]` 控制器層級，則 `[Authorize]` 會忽略相同控制器上的任何屬性 (或它) 內的任何動作。

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a>授權屬性和 Razor 頁面

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>***無法*** 套用至 Razor 頁面處理常式。 例如， `[Authorize]` 無法套用至 `OnGet` 、 `OnPost` 或任何其他頁面處理常式。 針對不同的處理常式，請考慮針對具有不同授權需求的頁面使用 ASP.NET Core MVC 控制器。

下列兩種方法可以用來將授權套用至 Razor 頁面處理常式方法：

* 針對需要不同授權的頁面處理常式，請使用不同的頁面。 將共用內容移至一或多個 [部分的視圖](xref:mvc/views/partial)。 可能的話，這是建議的方法。
* 對於必須共用通用頁面的內容，請撰寫可執行授權做為 [>iasyncpagefilter OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)一部分的篩選準則。 [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub 專案會示範這種方法：
  * [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs)會實行授權篩選準則：[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16)屬性會套用至 `OnGet` 頁面處理常式：[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth)範例 ***方法不會：***
> * 以套用至頁面、頁面模型或全域的授權屬性撰寫。 當您有一或多個 `AuthorizeAttribute` 實例也套用至頁面時，撰寫授權屬性會導致驗證和授權執行多次 `AuthorizeFilter` 。
> * 搭配 ASP.NET Core 驗證和授權系統的其餘部分一起使用。 您必須確認應用程式的使用方式正確。

沒有計劃支援 `AuthorizeAttribute` on Razor 頁面處理常式。 
