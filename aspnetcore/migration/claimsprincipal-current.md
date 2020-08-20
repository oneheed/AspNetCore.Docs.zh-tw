---
title: 從 ClaimsPrincipal 遷移
author: mjrousos
description: 瞭解如何從 ClaimsPrincipal 遷移，以在 ASP.NET Core 中取得目前已驗證的使用者身分識別和宣告。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 426fd90374a460cb283d0d3ba921e1312fb17940
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634068"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>從 ClaimsPrincipal 遷移

在 ASP.NET 4.x 專案中，通常會使用 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current) 來取得目前已驗證使用者的身分識別和宣告。 在 ASP.NET Core 中，不再設定這個屬性。 根據此程式碼需要更新，以透過不同的方式取得目前已驗證的使用者身分識別。

## <a name="context-specific-state-instead-of-static-state"></a>特定內容狀態，而不是靜態狀態

使用 ASP.NET Core 時，和的值都 `ClaimsPrincipal.Current` `Thread.CurrentPrincipal` 不會設定。 這些屬性都代表靜態狀態，ASP.NET Core 通常會避免。 相反地，ASP.NET Core 會使用相依性 [插入](xref:fundamentals/dependency-injection) (DI) 來提供相依性，例如目前使用者的身分識別。 從 DI 取得目前使用者的身分識別也會更容易測試，因為可以輕鬆地插入測試身分識別。

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>在 ASP.NET Core 應用程式中取出目前的使用者

有幾個選項可以用來在 ASP.NET Core 中抓取目前已驗證的使用者， `ClaimsPrincipal` 以取代 `ClaimsPrincipal.Current` ：

* **ControllerBase。** MVC 控制器可以使用其 [使用者](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) 屬性來存取目前已驗證的使用者。
* **HttpCoNtext. 使用者**。 可存取目前 `HttpContext` (中介軟體的元件，例如) 可以從 HttpCoNtext 取得目前的使用者 `ClaimsPrincipal` 。 [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)
* **從呼叫端傳入**。 沒有目前存取權的程式庫 `HttpContext` 通常會從控制器或中介軟體元件呼叫，並且可以將目前使用者的身分識別做為引數傳遞。
* **>iHTTPcoNtextaccessor**。 正在遷移至 ASP.NET Core 的專案可能太大，無法輕鬆地將目前使用者的身分識別傳遞至所有必要的位置。 在這種情況下， [>iHTTPcoNtextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) 可用來作為因應措施。 `IHttpContextAccessor` 可以存取目前的 (（ `HttpContext` 如果有的話）) 。 如果正在使用 DI，請參閱 <xref:fundamentals/httpcontext> 。 在程式碼中取得目前使用者的身分識別，但尚未更新為使用 ASP.NET Core 的 DI 驅動架構的短期解決方案如下：

  * 在 `IHttpContextAccessor` 中呼叫 [AddHttpCoNtextAccessor](https://github.com/aspnet/Hosting/issues/793) ，以便在 DI 容器中使用 `Startup.ConfigureServices` 。
  * 在啟動期間取得的實例 `IHttpContextAccessor` ，並將它儲存在靜態變數中。 此實例可供先前從靜態屬性取得目前使用者的程式碼使用。
  * 使用取得目前使用者的 `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User` 。 如果在 HTTP 要求的內容之外使用此程式碼，則 `HttpContext` 為 null。

最後一個選項（使用 `IHttpContextAccessor` 儲存在靜態變數中的實例），與將插入的相依性插入靜態相依性的 ASP.NET Core 準則相反。 規劃最後改為 `IHttpContextAccessor` 從 DI 取出實例。 但是，當您遷移使用的大型現有 ASP.NET 應用程式時，靜態協助程式可能是很有用的橋樑 `ClaimsPrincipal.Current` 。
