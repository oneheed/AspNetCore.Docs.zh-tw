---
title: 'ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 範例'
author: rick-anderson
description: 'ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 範例'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051547"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="807a1-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 範例</span><span class="sxs-lookup"><span data-stu-id="807a1-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: sample</span></span>

<span data-ttu-id="807a1-104">ASP.NET Core 2.1 內建 [SameSite](https://www.owasp.org/index.php/SameSite) 屬性的支援，但它已寫入原始的標準。</span><span class="sxs-lookup"><span data-stu-id="807a1-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="807a1-105">修補後的 [行為](https://github.com/dotnet/aspnetcore/issues/8212) 會變更的意義 `SameSite.None` ，以發出 sameSite 屬性的值 `None` ，而不是完全不發出值。</span><span class="sxs-lookup"><span data-stu-id="807a1-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="807a1-106">如果您不想發出值，您可以將屬性設定 `SameSite` :::no-loc(cookie)::: 為-1。</span><span class="sxs-lookup"><span data-stu-id="807a1-106">If you want to not emit the value you can set the `SameSite` property on a :::no-loc(cookie)::: to -1.</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="807a1-107">寫入 SameSite 屬性</span><span class="sxs-lookup"><span data-stu-id="807a1-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="807a1-108">以下是如何在上撰寫 SameSite 屬性的範例 :::no-loc(cookie)::: ：</span><span class="sxs-lookup"><span data-stu-id="807a1-108">Following is an example of how to write a SameSite attribute on a :::no-loc(cookie)::::</span></span>

```c#
var :::no-loc(cookie):::Options = new :::no-loc(Cookie):::Options
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the :::no-loc(cookie)::: to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the :::no-loc(cookie)::: to the response :::no-loc(cookie)::: collection
Response.:::no-loc(Cookie):::s.Append(:::no-loc(Cookie):::Name, ":::no-loc(cookie):::Value", :::no-loc(cookie):::Options);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="807a1-109">設定 :::no-loc(Cookie)::: 驗證和會話狀態 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="807a1-109">Setting :::no-loc(Cookie)::: Authentication and Session State :::no-loc(cookie):::s</span></span>

<span data-ttu-id="807a1-110">:::no-loc(Cookie)::: 驗證、會話狀態和 [各種其他元件](../samesite.md?view=aspnetcore-2.1) 會透過選項設定其 sameSite 選項 :::no-loc(Cookie)::: ，例如</span><span class="sxs-lookup"><span data-stu-id="807a1-110">:::no-loc(Cookie)::: authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via :::no-loc(Cookie)::: options, for example</span></span>

```c#
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
        options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
        options.:::no-loc(Cookie):::.IsEssential = true;
    });

services.AddSession(options =>
{
    options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
    options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
    options.:::no-loc(Cookie):::.IsEssential = true;
});
```

<span data-ttu-id="807a1-111">在上述程式碼中， :::no-loc(cookie)::: 驗證和會話狀態都會將其 sameSite 屬性設定為 `None` ，發出具有值的屬性， `None` 並同時將安全屬性設定為 true。</span><span class="sxs-lookup"><span data-stu-id="807a1-111">In the preceding code, both :::no-loc(cookie)::: authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="807a1-112">執行範例</span><span class="sxs-lookup"><span data-stu-id="807a1-112">Run the sample</span></span>

<span data-ttu-id="807a1-113">如果您執行 [範例專案](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)，請在初始頁面上載入您的瀏覽器偵錯工具，並使用它來查看 :::no-loc(cookie)::: 網站的集合。</span><span class="sxs-lookup"><span data-stu-id="807a1-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the :::no-loc(cookie)::: collection for the site.</span></span> <span data-ttu-id="807a1-114">若要在 Edge 和 Chrome 中這麼做，請按一下 [] 索引標籤，然後在 `F12` `Application` 區段中的選項下按一下網站 URL `:::no-loc(Cookie):::s` `Storage` 。</span><span class="sxs-lookup"><span data-stu-id="807a1-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `:::no-loc(Cookie):::s` option in the `Storage` section.</span></span>

![瀏覽器偵錯工具：：：無 loc (Cookie) ：：： List](BrowserDebugger.png)

<span data-ttu-id="807a1-116">您可以在上方的影像中看到 :::no-loc(cookie)::: ，當您按一下 [建立 SameSite] 按鈕時，範例所建立的 :::no-loc(Cookie)::: SameSite 屬性值，與 `Lax` [範例程式碼](#sampleCode)中所設定的值相符。</span><span class="sxs-lookup"><span data-stu-id="807a1-116">You can see from the image above that the :::no-loc(cookie)::: created by the sample when you click the "Create SameSite :::no-loc(Cookie):::" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="807a1-117">攔截 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="807a1-117">Intercepting :::no-loc(cookie):::s</span></span>

<span data-ttu-id="807a1-118">為了攔截 :::no-loc(cookie)::: s，若要根據使用者的瀏覽器代理程式中的支援來調整無值，您必須使用 `:::no-loc(Cookie):::Policy` 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="807a1-118">In order to intercept :::no-loc(cookie):::s, to adjust the none value according to its support in the user's browser agent you must use the `:::no-loc(Cookie):::Policy` middleware.</span></span> <span data-ttu-id="807a1-119">這必須放入 HTTP 要求管線中， **才能** 在 :::no-loc(cookie)::: 中寫入及設定的任何元件 `ConfigureServices()` 。</span><span class="sxs-lookup"><span data-stu-id="807a1-119">This must be placed into the http request pipeline **before** any components that write :::no-loc(cookie):::s and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="807a1-120">若要將它插入管線中，請使用 `app.Use:::no-loc(Cookie):::Policy()` Startup.cs 中的 `Configure(IApplicationBuilder, IHostingEnvironment)` 方法。 [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)</span><span class="sxs-lookup"><span data-stu-id="807a1-120">To insert it into the pipeline use `app.Use:::no-loc(Cookie):::Policy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="807a1-121">例如：</span><span class="sxs-lookup"><span data-stu-id="807a1-121">For example:</span></span>

```c#
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
    app.Use:::no-loc(Cookie):::Policy();
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

<span data-ttu-id="807a1-122">然後在中 `ConfigureServices(IServiceCollection services)` 設定 :::no-loc(cookie)::: 原則，以在 :::no-loc(cookie)::: 附加或刪除時呼叫 helper 類別。</span><span class="sxs-lookup"><span data-stu-id="807a1-122">Then in the `ConfigureServices(IServiceCollection services)` configure the :::no-loc(cookie)::: policy to call out to a helper class when :::no-loc(cookie):::s are appended or deleted.</span></span> <span data-ttu-id="807a1-123">例如：</span><span class="sxs-lookup"><span data-stu-id="807a1-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<:::no-loc(Cookie):::PolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppend:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
        options.OnDelete:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
    });
}

private void CheckSameSite(HttpContext httpContext, :::no-loc(Cookie):::Options options)
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

<span data-ttu-id="807a1-124">Helper 函數 `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)` ：</span><span class="sxs-lookup"><span data-stu-id="807a1-124">The helper function `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)`:</span></span>

* <span data-ttu-id="807a1-125">當將 :::no-loc(cookie)::: s 附加至要求或從要求中刪除時，會呼叫。</span><span class="sxs-lookup"><span data-stu-id="807a1-125">Is called when :::no-loc(cookie):::s are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="807a1-126">檢查 `SameSite` 屬性是否設定為 `None` 。</span><span class="sxs-lookup"><span data-stu-id="807a1-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="807a1-127">如果 `SameSite` 設定為 `None` ，且目前的使用者代理程式已知不支援 none 屬性值，則為。</span><span class="sxs-lookup"><span data-stu-id="807a1-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="807a1-128">檢查是使用 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) 類別來完成：</span><span class="sxs-lookup"><span data-stu-id="807a1-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="807a1-129">藉 `SameSite` 由將屬性設定為，將設定為不發出值 `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="807a1-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="807a1-130">目標 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="807a1-130">Targeting .NET Framework</span></span>

<span data-ttu-id="807a1-131">ASP.NET Core 和 System. Web (ASP.NET 傳統) 具有獨立的 SameSite 執行。</span><span class="sxs-lookup"><span data-stu-id="807a1-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="807a1-132">如果使用 ASP.NET Core 也不需要 SameSite KB 修補程式， ( .NET 4.7.2) 套用至 ASP.NET Core，就不需要進行 .NET Framework 的 KB 修補程式。</span><span class="sxs-lookup"><span data-stu-id="807a1-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="807a1-133">.NET 上的 ASP.NET Core 需要更新 nuget 套件相依性，才能取得適當的修正程式。</span><span class="sxs-lookup"><span data-stu-id="807a1-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="807a1-134">若要取得 .NET Framework 的 ASP.NET Core 變更，請確定您有 (2.1.14 或更新版本2.1 版本) 的已修補套件和版本的直接參考。</span><span class="sxs-lookup"><span data-stu-id="807a1-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.:::no-loc(Cookie):::Policy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="807a1-135">相關資訊</span><span class="sxs-lookup"><span data-stu-id="807a1-135">More Information</span></span>
 
<span data-ttu-id="807a1-136">[Chrome 更新](https://www.chromium.org/updates/same-site) 
[ASP.NET Core SameSite 檔](../samesite.md?view=aspnetcore-2.1) 
[ASP.NET Core 2.1 SameSite 變更公告](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="807a1-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>