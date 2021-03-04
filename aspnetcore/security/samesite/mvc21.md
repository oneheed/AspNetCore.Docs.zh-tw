---
title: ASP.NET Core 2.1 MVC SameSite cookie 範例
author: rick-anderson
description: ASP.NET Core 2.1 MVC SameSite cookie 範例
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 8f819d283e136a63ad9f82d6432a93866210b36b
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110101"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>ASP.NET Core 2.1 MVC SameSite cookie 範例

ASP.NET Core 2.1 具有 [SameSite](https://www.owasp.org/index.php/SameSite) 屬性的內建支援，但它已寫入原始的標準。 修補後的 [行為](https://github.com/dotnet/aspnetcore/issues/8212) 會變更的意義 `SameSite.None` ，以發出 sameSite 屬性的值 `None` ，而不是完全不發出值。 如果您不想發出值，您可以將屬性設定 `SameSite` cookie 為-1。

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>寫入 SameSite 屬性

以下是如何在上撰寫 SameSite 屬性的範例 cookie ：

```csharp
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>設定 Cookie 驗證和會話狀態 cookie s

Cookie 驗證、會話狀態和 [各種其他元件](../samesite.md?view=aspnetcore-2.1) 會透過選項設定其 sameSite 選項 Cookie ，例如

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

在上述程式碼中， cookie 驗證和會話狀態都會將其 sameSite 屬性設定為 `None` ，發出具有值的屬性， `None` 並同時將安全屬性設定為 true。

### <a name="run-the-sample"></a>執行範例

如果您執行 [範例專案](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)，請在初始頁面上載入您的瀏覽器偵錯工具，並使用它來查看 cookie 網站的集合。 若要在 Edge 和 Chrome 中這麼做，請按一下 [] 索引標籤，然後在 `F12` `Application` 區段中的選項下按一下網站 URL `Cookies` `Storage` 。

![瀏覽器偵錯工具：：：無 loc (Cookie) ：：： List](BrowserDebugger.png)

您可以在上方的影像中看到 cookie ，當您按一下 [建立 SameSite] 按鈕時，範例所建立的 Cookie SameSite 屬性值，與 `Lax` [範例程式碼](#sampleCode)中所設定的值相符。

## <a name="intercepting-cookies"></a><a name="interception"></a>攔截 cookie s

為了攔截 cookie s，若要根據使用者的瀏覽器代理程式中的支援來調整無值，您必須使用 `CookiePolicy` 中介軟體。 這必須放入 HTTP 要求管線中， **才能** 在 cookie 中寫入及設定的任何元件 `ConfigureServices()` 。

若要將它插入管線中，請使用 `app.UseCookiePolicy()` Startup.cs 中的 `Configure(IApplicationBuilder, IHostingEnvironment)` 方法。 [](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs) 例如：

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

然後在中 `ConfigureServices(IServiceCollection services)` 設定 cookie 原則，以在 cookie 附加或刪除時呼叫 helper 類別。 例如：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

Helper 函數 `CheckSameSite(HttpContext, CookieOptions)` ：

* 當將 cookie s 附加至要求或從要求中刪除時，會呼叫。
* 檢查 `SameSite` 屬性是否設定為 `None` 。
* 如果 `SameSite` 設定為 `None` ，且目前的使用者代理程式已知不支援 none 屬性值，則為。 檢查是使用 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) 類別來完成：
  * 藉 `SameSite` 由將屬性設定為，將設定為不發出值 `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>以 .NET Framework 為目標

ASP.NET Core 和 System.object (ASP.NET 4.x) 具有 SameSite 的獨立。 如果使用 ASP.NET Core ( 或 SameSite 不需要 .net framework 4.7.2) 適用于 ASP.NET Core 的 KB 修補程式，則不需要 .NET Framework 的 KB 修補程式。

.NET 上的 ASP.NET Core 需要更新 NuGet 套件相依性，才能取得適當的修正程式。

若要取得 .NET Framework 的 ASP.NET Core 變更，請確定您有修補套件和版本的直接參考 (2.1.14 或更新版本的2.1 版本) 。

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>相關資訊
 
[Chrome 更新](https://www.chromium.org/updates/same-site) 
[ASP.NET Core SameSite 檔](../samesite.md?view=aspnetcore-2.1) 
[ASP.NET Core 2.1 SameSite 變更公告](https://github.com/dotnet/aspnetcore/issues/8212)