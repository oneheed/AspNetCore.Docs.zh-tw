---
title: ASP.NET Core 中的靜態檔案
author: rick-anderson
description: 了解如何在 ASP.NET Core Web 應用程式中提供靜態檔案、保護其安全，並設定靜態檔案裝載中介軟體的行為。
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: fb5ef59a74a768a1c202b49872b8a10327c96ff8
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413453"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="41164-103">ASP.NET Core 中的靜態檔案</span><span class="sxs-lookup"><span data-stu-id="41164-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41164-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="41164-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="41164-105">靜態檔案（例如 HTML、CSS、影像和 JavaScript）是 ASP.NET Core 應用程式預設會直接提供給用戶端的資產。</span><span class="sxs-lookup"><span data-stu-id="41164-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="41164-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="41164-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="41164-107">提供靜態檔案</span><span class="sxs-lookup"><span data-stu-id="41164-107">Serve static files</span></span>

<span data-ttu-id="41164-108">靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。</span><span class="sxs-lookup"><span data-stu-id="41164-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="41164-109">預設目錄是 `{content root}/wwwroot` ，但您可以使用方法來變更它 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 。</span><span class="sxs-lookup"><span data-stu-id="41164-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="41164-110">如需詳細資訊，請參閱 [內容根目錄](xref:fundamentals/index#content-root) 和 [Web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="41164-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="41164-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 方法可將內容根目錄設定為目前的目錄：</span><span class="sxs-lookup"><span data-stu-id="41164-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="41164-112">上述程式碼是使用 web 應用程式範本所建立。</span><span class="sxs-lookup"><span data-stu-id="41164-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="41164-113">靜態檔案可透過相對於 [web 根目錄](xref:fundamentals/index#web-root)的路徑來存取。</span><span class="sxs-lookup"><span data-stu-id="41164-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="41164-114">例如， **Web 應用程式** 專案範本包含資料夾中的數個資料夾 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="41164-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="41164-115">請考慮建立 *wwwroot/images* 資料夾，並新增 *wwwroot/images/MyImage.jpg* 檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="41164-116">用來存取資料夾中檔案的 URI 格式 `images` 為 `https://<hostname>/images/<image_file_name>` 。</span><span class="sxs-lookup"><span data-stu-id="41164-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="41164-117">例如， `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="41164-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="41164-118">在 web 根目錄中提供檔案</span><span class="sxs-lookup"><span data-stu-id="41164-118">Serve files in web root</span></span>

<span data-ttu-id="41164-119">預設的 web 應用程式範本 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 會在中呼叫方法 `Startup.Configure` ，以啟用靜態檔案的服務：</span><span class="sxs-lookup"><span data-stu-id="41164-119">The default web app templates call the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="41164-120">無參數方法多載會 `UseStaticFiles` 將 [web 根目錄](xref:fundamentals/index#web-root) 中的檔案標示為 servable。</span><span class="sxs-lookup"><span data-stu-id="41164-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="41164-121">下列標記會參考 *wwwroot/images/MyImage.jpg*：</span><span class="sxs-lookup"><span data-stu-id="41164-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="41164-122">在上述程式碼中，波形字元會 `~/` 指向 [web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="41164-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="41164-123">提供 Web 根目錄外的檔案</span><span class="sxs-lookup"><span data-stu-id="41164-123">Serve files outside of web root</span></span>

<span data-ttu-id="41164-124">請考慮要提供的靜態檔案位於 [web 根目錄](xref:fundamentals/index#web-root)外部的目錄階層：</span><span class="sxs-lookup"><span data-stu-id="41164-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="41164-125">要求可以藉由設定靜態檔案中介軟體來存取檔案，如下所示 `red-rose.jpg` ：</span><span class="sxs-lookup"><span data-stu-id="41164-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="41164-126">上述程式碼會透過 *StaticFiles* URI 區段公開 *MyStaticFiles* 目錄階層。</span><span class="sxs-lookup"><span data-stu-id="41164-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="41164-127">`https://<hostname>/StaticFiles/images/red-rose.jpg`服務 *red-rose.jpg* 檔案的要求。</span><span class="sxs-lookup"><span data-stu-id="41164-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="41164-128">下列標記參考 *>mystaticfiles/images/red-rose.jpg*：</span><span class="sxs-lookup"><span data-stu-id="41164-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="41164-129">設定 HTTP 回應標頭</span><span class="sxs-lookup"><span data-stu-id="41164-129">Set HTTP response headers</span></span>

<span data-ttu-id="41164-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>物件可以用來設定 HTTP 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="41164-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="41164-131">除了從 [web 根目錄](xref:fundamentals/index#web-root)設定靜態檔案服務之外，下列程式碼還會設定 `Cache-Control` 標頭：</span><span class="sxs-lookup"><span data-stu-id="41164-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="41164-132">靜態檔案可公開快取600秒：</span><span class="sxs-lookup"><span data-stu-id="41164-132">Static files are publicly cacheable for 600 seconds:</span></span>

![已新增顯示「快取控制」標頭的回應標頭](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="41164-134">靜態檔案授權</span><span class="sxs-lookup"><span data-stu-id="41164-134">Static file authorization</span></span>

<span data-ttu-id="41164-135">ASP.NET 核心範本會 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 在呼叫之前呼叫 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 。</span><span class="sxs-lookup"><span data-stu-id="41164-135">The ASP.NET Core templates call <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> before calling <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span></span> <span data-ttu-id="41164-136">大部分的應用程式會遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="41164-136">Most apps follow this pattern.</span></span> <span data-ttu-id="41164-137">在授權中介軟體之前呼叫靜態檔案中介軟體：</span><span class="sxs-lookup"><span data-stu-id="41164-137">When the Static File Middleware is called before the authorization middleware:</span></span>

  * <span data-ttu-id="41164-138">靜態檔案不會執行任何授權檢查。</span><span class="sxs-lookup"><span data-stu-id="41164-138">No authorization checks are performed on the static files.</span></span>
  * <span data-ttu-id="41164-139">靜態檔案中介軟體所提供的靜態檔案（例如這些檔案中介軟體）可 `wwwroot` 公開存取。</span><span class="sxs-lookup"><span data-stu-id="41164-139">Static files served by the Static File Middleware, such as those those under `wwwroot`, are publicly accessible.</span></span>
  
<span data-ttu-id="41164-140">若要根據授權提供靜態檔案：</span><span class="sxs-lookup"><span data-stu-id="41164-140">To serve static files based on authorization:</span></span>

  * <span data-ttu-id="41164-141">將它們儲存在之外 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="41164-141">Store them outside of `wwwroot`.</span></span>
  * <span data-ttu-id="41164-142">呼叫 `UseStaticFiles` 之後，指定路徑 `UseAuthorization` 。</span><span class="sxs-lookup"><span data-stu-id="41164-142">Call `UseStaticFiles`, specifying a path, after calling `UseAuthorization`.</span></span>
  * <span data-ttu-id="41164-143">設定 [fallback 授權原則](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。</span><span class="sxs-lookup"><span data-stu-id="41164-143">Set the [fallback authorization policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  <span data-ttu-id="41164-144">在上述程式碼中，fallback 授權原則會要求 ***所有*** 使用者進行驗證。</span><span class="sxs-lookup"><span data-stu-id="41164-144">In the preceding code, the fallback authorization policy requires ***all*** users to be authenticated.</span></span> <span data-ttu-id="41164-145">指定自己的授權需求的端點（例如控制器、頁面等） Razor 不會使用 fallback 授權原則。</span><span class="sxs-lookup"><span data-stu-id="41164-145">Endpoints such as controllers, Razor Pages, etc that specify their own authorization requirements don't use the fallback authorization policy.</span></span> <span data-ttu-id="41164-146">例如，使用 Razor 或的頁面、控制器或動作方法，會使用已套用 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 的授權屬性，而非 fallback 授權原則。</span><span class="sxs-lookup"><span data-stu-id="41164-146">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authorization attribute rather than the fallback authorization policy.</span></span>

  <span data-ttu-id="41164-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 加入 <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 目前的實例，這會強制驗證目前的使用者。</span><span class="sxs-lookup"><span data-stu-id="41164-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

  <span data-ttu-id="41164-148">下的靜態資產可 `wwwroot` 公開存取，因為預設靜態檔案中介軟體 (`app.UseStaticFiles();`) 會先呼叫 `UseAuthentication` 。</span><span class="sxs-lookup"><span data-stu-id="41164-148">Static assets under `wwwroot` are publicly accessible because the default Static File Middleware (`app.UseStaticFiles();`) is called before `UseAuthentication`.</span></span> <span data-ttu-id="41164-149">*>mystaticfiles* 資料夾中的靜態資產需要驗證。</span><span class="sxs-lookup"><span data-stu-id="41164-149">Static assets in the *MyStaticFiles* folder require authentication.</span></span> <span data-ttu-id="41164-150">[範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples)會示範這一點。</span><span class="sxs-lookup"><span data-stu-id="41164-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) demonstrates this.</span></span>

<span data-ttu-id="41164-151">根據授權提供檔案的替代方法是：</span><span class="sxs-lookup"><span data-stu-id="41164-151">An alternative approach to serve files based on authorization is to:</span></span>

  * <span data-ttu-id="41164-152">將它們儲存 `wwwroot` 在靜態檔案中介軟體的外部，以及任何可存取的目錄。</span><span class="sxs-lookup"><span data-stu-id="41164-152">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
  * <span data-ttu-id="41164-153">透過套用授權的動作方法來提供服務，並傳回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 物件：</span><span class="sxs-lookup"><span data-stu-id="41164-153">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="41164-154">瀏覽目錄</span><span class="sxs-lookup"><span data-stu-id="41164-154">Directory browsing</span></span>

<span data-ttu-id="41164-155">瀏覽目錄允許在指定的目錄中列出目錄。</span><span class="sxs-lookup"><span data-stu-id="41164-155">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="41164-156">基於安全性考慮，預設會停用瀏覽目錄功能。</span><span class="sxs-lookup"><span data-stu-id="41164-156">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="41164-157">如需詳細資訊，請參閱 [考慮](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="41164-157">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="41164-158">啟用瀏覽目錄：</span><span class="sxs-lookup"><span data-stu-id="41164-158">Enable directory browsing with:</span></span>

* <span data-ttu-id="41164-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 在中 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="41164-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="41164-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 在中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="41164-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="41164-161">上述程式碼允許使用 URL 流覽 *wwwroot/images* 資料夾的目錄 `https://<hostname>/MyImages` ，其中包含每個檔案和資料夾的連結：</span><span class="sxs-lookup"><span data-stu-id="41164-161">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![目錄瀏覽](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="41164-163">提供預設檔</span><span class="sxs-lookup"><span data-stu-id="41164-163">Serve default documents</span></span>

<span data-ttu-id="41164-164">設定預設頁面可為訪客提供網站上的起點。</span><span class="sxs-lookup"><span data-stu-id="41164-164">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="41164-165">若要從沒有完整 URI 的預設頁面提供服務 `wwwroot` ，請呼叫 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="41164-165">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="41164-166">您必須在 `UseStaticFiles` 之前先呼叫 `UseDefaultFiles`，才能提供預設的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-166">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="41164-167">`UseDefaultFiles` 是不提供檔案的 URL 重寫工具。</span><span class="sxs-lookup"><span data-stu-id="41164-167">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="41164-168">使用時 `UseDefaultFiles` ，會要求搜尋中的資料夾 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="41164-168">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="41164-169">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="41164-169">*default.htm*</span></span>
* <span data-ttu-id="41164-170">*default.html*</span><span class="sxs-lookup"><span data-stu-id="41164-170">*default.html*</span></span>
* <span data-ttu-id="41164-171">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="41164-171">*index.htm*</span></span>
* <span data-ttu-id="41164-172">*index.html*</span><span class="sxs-lookup"><span data-stu-id="41164-172">*index.html*</span></span>

<span data-ttu-id="41164-173">系統會提供從清單中找到的第一個檔案，就像提出的要求是完整 URI 一樣。</span><span class="sxs-lookup"><span data-stu-id="41164-173">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="41164-174">瀏覽器 URL 仍會繼續反應要求的 URI。</span><span class="sxs-lookup"><span data-stu-id="41164-174">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="41164-175">下列程式碼會將預設檔案名稱變更為 *mydefault.html*：</span><span class="sxs-lookup"><span data-stu-id="41164-175">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="41164-176">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="41164-176">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="41164-177">預設檔的 UseFileServer</span><span class="sxs-lookup"><span data-stu-id="41164-177">UseFileServer for default documents</span></span>

<span data-ttu-id="41164-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 結合、和的功能（ `UseStaticFiles` `UseDefaultFiles` 選擇性） `UseDirectoryBrowser` 。</span><span class="sxs-lookup"><span data-stu-id="41164-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="41164-179">呼叫 `app.UseFileServer` 以啟用靜態檔案和預設檔案的服務。</span><span class="sxs-lookup"><span data-stu-id="41164-179">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="41164-180">未啟用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="41164-180">Directory browsing isn't enabled.</span></span> <span data-ttu-id="41164-181">下列程式碼顯示 `Startup.Configure` `UseFileServer` ：</span><span class="sxs-lookup"><span data-stu-id="41164-181">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="41164-182">下列程式碼可讓您提供靜態檔案、預設檔案和瀏覽目錄：</span><span class="sxs-lookup"><span data-stu-id="41164-182">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="41164-183">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="41164-183">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="41164-184">請考慮下列目錄階層：</span><span class="sxs-lookup"><span data-stu-id="41164-184">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="41164-185">下列程式碼可讓您提供靜態檔案、預設檔案，以及的瀏覽目錄 `MyStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="41164-185">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="41164-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 當 `EnableDirectoryBrowsing` 屬性值為時，必須呼叫 `true` 。</span><span class="sxs-lookup"><span data-stu-id="41164-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="41164-187">使用檔案階層和上述程式碼時，URL 解析方式如下：</span><span class="sxs-lookup"><span data-stu-id="41164-187">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="41164-188">URI</span><span class="sxs-lookup"><span data-stu-id="41164-188">URI</span></span>            |      <span data-ttu-id="41164-189">回應</span><span class="sxs-lookup"><span data-stu-id="41164-189">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="41164-190">*>mystaticfiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="41164-190">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="41164-191">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="41164-191">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="41164-192">如果 *>mystaticfiles* 目錄中沒有預設名稱的檔案，則會傳回 `https://<hostname>/StaticFiles` 具有可點選連結的目錄清單：</span><span class="sxs-lookup"><span data-stu-id="41164-192">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![靜態檔案清單](static-files/_static/db2.png)

<span data-ttu-id="41164-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 然後 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 從目標 uri 執行用戶端重新導向，不含尾端的 `/`  目標 uri `/` 。</span><span class="sxs-lookup"><span data-stu-id="41164-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="41164-195">例如，從 `https://<hostname>/StaticFiles` 到 `https://<hostname>/StaticFiles/` 。</span><span class="sxs-lookup"><span data-stu-id="41164-195">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="41164-196">*StaticFiles* 目錄內的相對 url 無效，不含尾端斜線 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="41164-196">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="41164-197">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="41164-197">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="41164-198"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>類別包含 `Mappings` 屬性，可作為 MIME 內容類型的副檔名對應。</span><span class="sxs-lookup"><span data-stu-id="41164-198">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="41164-199">在下列範例中，有數個副檔名對應至已知的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="41164-199">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="41164-200">會取代 *.rtf* 副檔名，而且會移除 *. （.* ）：</span><span class="sxs-lookup"><span data-stu-id="41164-200">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="41164-201">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="41164-201">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="41164-202">請參閱 [MIME 內容類型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="41164-202">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="41164-203">非標準的內容類型</span><span class="sxs-lookup"><span data-stu-id="41164-203">Non-standard content types</span></span>

<span data-ttu-id="41164-204">靜態檔案中介軟體瞭解將近400個已知的檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="41164-204">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="41164-205">如果使用者要求具有未知檔案類型的檔案，則靜態檔案中介軟體會將要求傳遞至管線中的下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="41164-205">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="41164-206">如果沒有中介軟體處理要求，則會傳回「404 找不到」回應。</span><span class="sxs-lookup"><span data-stu-id="41164-206">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="41164-207">如果啟用目錄瀏覽功能，則會在目錄清單中顯示檔案的連結。</span><span class="sxs-lookup"><span data-stu-id="41164-207">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="41164-208">下列程式碼可讓您提供未知的類型，並會將未知的檔案轉譯為影像：</span><span class="sxs-lookup"><span data-stu-id="41164-208">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="41164-209">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="41164-209">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="41164-210">使用上述程式碼時，系統會以影像來回應具有未知內容類型的檔案要求。</span><span class="sxs-lookup"><span data-stu-id="41164-210">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="41164-211">啟用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="41164-211">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="41164-212">預設為停用，亦不建議您使用。</span><span class="sxs-lookup"><span data-stu-id="41164-212">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="41164-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 可提供更安全的替代方法，來提供非標準副檔名的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="41164-214">提供多個位置的檔案</span><span class="sxs-lookup"><span data-stu-id="41164-214">Serve files from multiple locations</span></span>

<span data-ttu-id="41164-215">`UseStaticFiles` 而且 `UseFileServer` 預設為指向的檔案提供者 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="41164-215">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="41164-216">`UseStaticFiles`和其他檔案提供者可以提供和的其他實例， `UseFileServer` 以提供來自其他位置的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-216">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="41164-217">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="41164-217">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="41164-218">靜態檔案的安全性考慮</span><span class="sxs-lookup"><span data-stu-id="41164-218">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="41164-219">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能會導致洩漏祕密。</span><span class="sxs-lookup"><span data-stu-id="41164-219">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="41164-220">強烈建議您在生產環境中停用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="41164-220">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="41164-221">透過 `UseStaticFiles` 或 `UseDirectoryBrowser`，仔細檢閱要啟用哪些目錄。</span><span class="sxs-lookup"><span data-stu-id="41164-221">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="41164-222">因為整個目錄和其子目錄都可供公開存取。</span><span class="sxs-lookup"><span data-stu-id="41164-222">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="41164-223">儲存適合在專用目錄中提供公用的檔案，例如 `<content_root>/wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="41164-223">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="41164-224">將這些檔案與 MVC 視圖、 Razor 頁面、設定檔等區隔開。</span><span class="sxs-lookup"><span data-stu-id="41164-224">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="41164-225">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公開內容的 URL 可能有區分大小寫，並受限於基礎檔案系統的字元限制。</span><span class="sxs-lookup"><span data-stu-id="41164-225">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="41164-226">例如，Windows 不區分大小寫，但 macOS 和 Linux 則不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="41164-226">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="41164-227">裝載於 IIS 中的 ASP.NET Core 應用程式會使用 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)，將所有要求轉送給應用程式 (包括靜態檔案要求)，</span><span class="sxs-lookup"><span data-stu-id="41164-227">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="41164-228">未使用 IIS 靜態檔案處理常式，且沒有機會處理要求。</span><span class="sxs-lookup"><span data-stu-id="41164-228">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="41164-229">請完成 IIS 管理員中的下列步驟，以移除伺服器或網站層級的 IIS 靜態檔案處理常式：</span><span class="sxs-lookup"><span data-stu-id="41164-229">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="41164-230">巡覽至 [模組] 功能。</span><span class="sxs-lookup"><span data-stu-id="41164-230">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="41164-231">選取清單中的 **StaticFileModule**。</span><span class="sxs-lookup"><span data-stu-id="41164-231">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="41164-232">按一下 [動作] 側邊欄中的 [移除]。</span><span class="sxs-lookup"><span data-stu-id="41164-232">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="41164-233">如果已啟用 IIS 靜態檔案處理常式，**但是** 未正確設定 ASP.NET Core 模組，仍可提供靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-233">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="41164-234">舉例來說，未部署 *web.config* 檔案時可能會發生上述情況。</span><span class="sxs-lookup"><span data-stu-id="41164-234">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="41164-235">將程式碼檔案（包括 *.cs* 和 *cshtml*）放在應用程式專案的 [web 根目錄](xref:fundamentals/index#web-root)外部。</span><span class="sxs-lookup"><span data-stu-id="41164-235">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="41164-236">如此一來，即會建立應用程式的用戶端內容與伺服器端程式碼之間的邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="41164-236">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="41164-237">這樣可以防止伺服器端程式碼外洩。</span><span class="sxs-lookup"><span data-stu-id="41164-237">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41164-238">其他資源</span><span class="sxs-lookup"><span data-stu-id="41164-238">Additional resources</span></span>

* [<span data-ttu-id="41164-239">中介軟體</span><span class="sxs-lookup"><span data-stu-id="41164-239">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="41164-240">ASP.NET Core 簡介</span><span class="sxs-lookup"><span data-stu-id="41164-240">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="41164-241">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Scott Addie](https://twitter.com/Scott_Addie) 撰寫</span><span class="sxs-lookup"><span data-stu-id="41164-241">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="41164-242">HTML、CSS、影像和 JavaScript 這類靜態檔案都是 ASP.NET Core 應用程式直接提供給用戶端的資產。</span><span class="sxs-lookup"><span data-stu-id="41164-242">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="41164-243">您需要進行一些設定，才能提供這些檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-243">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="41164-244">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="41164-244">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="41164-245">提供靜態檔案</span><span class="sxs-lookup"><span data-stu-id="41164-245">Serve static files</span></span>

<span data-ttu-id="41164-246">靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。</span><span class="sxs-lookup"><span data-stu-id="41164-246">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="41164-247">預設目錄是 *{content root}/wwwroot*，但是可以透過方法來變更 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 。</span><span class="sxs-lookup"><span data-stu-id="41164-247">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="41164-248">如需詳細資訊，請參閱[內容根目錄](xref:fundamentals/index#content-root)和 [Web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="41164-248">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="41164-249">您必須讓應用程式的 Web 主機記住內容根目錄。</span><span class="sxs-lookup"><span data-stu-id="41164-249">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="41164-250">`WebHost.CreateDefaultBuilder` 方法可將內容根目錄設定為目前的目錄：</span><span class="sxs-lookup"><span data-stu-id="41164-250">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="41164-251">靜態檔案可透過相對於 [web 根目錄](xref:fundamentals/index#web-root)的路徑來存取。</span><span class="sxs-lookup"><span data-stu-id="41164-251">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="41164-252">例如， **Web 應用程式** 專案範本包含資料夾中的數個資料夾 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="41164-252">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="41164-253">用來存取 *images* 子資料夾中檔案的 URI 格式為 *HTTP:// \<server_address> /images/ \<image_file_name>*。</span><span class="sxs-lookup"><span data-stu-id="41164-253">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="41164-254">例如： *http://localhost:9189/images/banner3.svg* 。</span><span class="sxs-lookup"><span data-stu-id="41164-254">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="41164-255">如以 .NET Framework 為目標，請將 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="41164-255">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="41164-256">如以 .NET Core 為目標，[Microsoft.AspNetCore.All 中繼套件](xref:fundamentals/metapackage-app)會包含此套件。</span><span class="sxs-lookup"><span data-stu-id="41164-256">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="41164-257">設定 [中介軟體](xref:fundamentals/middleware/index)，以啟用靜態檔案的服務。</span><span class="sxs-lookup"><span data-stu-id="41164-257">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="41164-258">提供 Web 根目錄內的檔案</span><span class="sxs-lookup"><span data-stu-id="41164-258">Serve files inside of web root</span></span>

<span data-ttu-id="41164-259"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>在中叫用方法 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="41164-259">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="41164-260">無參數方法多載會 `UseStaticFiles` 將 [web 根目錄](xref:fundamentals/index#web-root) 中的檔案標示為 servable。</span><span class="sxs-lookup"><span data-stu-id="41164-260">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="41164-261">下列標記參考 *wwwroot/images/banner1.svg*：</span><span class="sxs-lookup"><span data-stu-id="41164-261">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="41164-262">在上述程式碼中，波形字元會 `~/` 指向 [web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="41164-262">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="41164-263">提供 Web 根目錄外的檔案</span><span class="sxs-lookup"><span data-stu-id="41164-263">Serve files outside of web root</span></span>

<span data-ttu-id="41164-264">請考慮要提供的靜態檔案位於 [web 根目錄](xref:fundamentals/index#web-root)外部的目錄階層：</span><span class="sxs-lookup"><span data-stu-id="41164-264">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="41164-265">透過設定靜態檔案中介軟體，可讓要求存取 *banner1.svg* 檔案，如下所示：</span><span class="sxs-lookup"><span data-stu-id="41164-265">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="41164-266">上述程式碼會透過 *StaticFiles* URI 區段公開 *MyStaticFiles* 目錄階層。</span><span class="sxs-lookup"><span data-stu-id="41164-266">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="41164-267">對 *Http:// \<server_address> /StaticFiles/images/banner1.svg* 的要求會提供 *>banner1.svg svg* 檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-267">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="41164-268">下列標記參考 *MyStaticFiles/images/banner1.svg*：</span><span class="sxs-lookup"><span data-stu-id="41164-268">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="41164-269">設定 HTTP 回應標頭</span><span class="sxs-lookup"><span data-stu-id="41164-269">Set HTTP response headers</span></span>

<span data-ttu-id="41164-270"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>物件可以用來設定 HTTP 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="41164-270">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="41164-271">除了從 [web 根目錄](xref:fundamentals/index#web-root)設定靜態檔案服務之外，下列程式碼還會設定 `Cache-Control` 標頭：</span><span class="sxs-lookup"><span data-stu-id="41164-271">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="41164-272"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>方法存在於[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)封裝中。</span><span class="sxs-lookup"><span data-stu-id="41164-272">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="41164-273">檔案已在開發環境中設為可公開快取 10 分鐘 (600 秒)：</span><span class="sxs-lookup"><span data-stu-id="41164-273">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![已新增顯示「快取控制」標頭的回應標頭](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="41164-275">靜態檔案授權</span><span class="sxs-lookup"><span data-stu-id="41164-275">Static file authorization</span></span>

<span data-ttu-id="41164-276">靜態檔案中介軟體不提供授權檢查。</span><span class="sxs-lookup"><span data-stu-id="41164-276">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="41164-277">其提供的所有檔案，包括在 *wwwroot* 下的檔案，皆可公開存取。</span><span class="sxs-lookup"><span data-stu-id="41164-277">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="41164-278">若要依據授權來提供檔案：</span><span class="sxs-lookup"><span data-stu-id="41164-278">To serve files based on authorization:</span></span>

* <span data-ttu-id="41164-279">請將它們儲存在 *wwwroot* 外部和可存取靜態檔案中介軟體的任何目錄。</span><span class="sxs-lookup"><span data-stu-id="41164-279">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="41164-280">透過動作方法，將它們提供給已套用授權的目標。</span><span class="sxs-lookup"><span data-stu-id="41164-280">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="41164-281">傳回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 物件：</span><span class="sxs-lookup"><span data-stu-id="41164-281">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="41164-282">啟用目錄瀏覽</span><span class="sxs-lookup"><span data-stu-id="41164-282">Enable directory browsing</span></span>

<span data-ttu-id="41164-283">您的 Web 應用程式使用者可藉由目錄瀏覽功能，查看目錄清單及指定目錄內的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-283">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="41164-284">基於安全性考量，預設為停用目錄瀏覽功能 (請參閱[考量](#considerations))。</span><span class="sxs-lookup"><span data-stu-id="41164-284">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="41164-285">藉由叫用中的方法來啟用瀏覽目錄 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="41164-285">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="41164-286">從下列方法叫用方法，以新增必要的服務 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="41164-286">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="41164-287">上述程式碼允許使用 URL *Http:// \<server_address> /MyImages* 來流覽 *wwwroot/images* 資料夾，其中包含每個檔案和資料夾的連結：</span><span class="sxs-lookup"><span data-stu-id="41164-287">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![目錄瀏覽](static-files/_static/dir-browse.png)

<span data-ttu-id="41164-289">如需了解啟用瀏覽功能時的安全性風險，請參閱[考量](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="41164-289">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="41164-290">請注意下列範例中的兩個 `UseStaticFiles` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="41164-290">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="41164-291">第一個呼叫可提供 *wwwroot* 資料夾中的靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-291">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="41164-292">第二次呼叫會使用 URL *Http:// \<server_address> /MyImages* 來啟用 *wwwroot/images* 資料夾的瀏覽目錄：</span><span class="sxs-lookup"><span data-stu-id="41164-292">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="41164-293">提供預設文件</span><span class="sxs-lookup"><span data-stu-id="41164-293">Serve a default document</span></span>

<span data-ttu-id="41164-294">設定預設的歡迎頁面，可讓訪客在瀏覽您的網站時有個合乎邏輯的起點。</span><span class="sxs-lookup"><span data-stu-id="41164-294">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="41164-295">若要在沒有使用者完整限定 URI 的情況下提供預設頁面，請 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 從呼叫方法 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="41164-295">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="41164-296">您必須在 `UseStaticFiles` 之前先呼叫 `UseDefaultFiles`，才能提供預設的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-296">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="41164-297">`UseDefaultFiles` 是 URL 重寫器，並非實際提供檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-297">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="41164-298">透過 `UseStaticFiles` 啟用靜態檔案中介軟體以提供檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-298">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="41164-299">使用 `UseDefaultFiles` 要求以搜尋資料夾：</span><span class="sxs-lookup"><span data-stu-id="41164-299">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="41164-300">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="41164-300">*default.htm*</span></span>
* <span data-ttu-id="41164-301">*default.html*</span><span class="sxs-lookup"><span data-stu-id="41164-301">*default.html*</span></span>
* <span data-ttu-id="41164-302">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="41164-302">*index.htm*</span></span>
* <span data-ttu-id="41164-303">*index.html*</span><span class="sxs-lookup"><span data-stu-id="41164-303">*index.html*</span></span>

<span data-ttu-id="41164-304">系統會提供從清單中找到的第一個檔案，就像提出的要求是完整 URI 一樣。</span><span class="sxs-lookup"><span data-stu-id="41164-304">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="41164-305">瀏覽器 URL 仍會繼續反應要求的 URI。</span><span class="sxs-lookup"><span data-stu-id="41164-305">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="41164-306">下列程式碼會將預設檔案名稱變更為 *mydefault.html*：</span><span class="sxs-lookup"><span data-stu-id="41164-306">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="41164-307">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="41164-307">UseFileServer</span></span>

<span data-ttu-id="41164-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 結合、和的功能（ `UseStaticFiles` `UseDefaultFiles` 選擇性） `UseDirectoryBrowser` 。</span><span class="sxs-lookup"><span data-stu-id="41164-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="41164-309">下列程式碼可提供靜態檔案和預設檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-309">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="41164-310">未啟用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="41164-310">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="41164-311">下列程式碼會啟用目錄瀏覽功能，以在無參數多載上進行建置：</span><span class="sxs-lookup"><span data-stu-id="41164-311">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="41164-312">請考慮下列目錄階層：</span><span class="sxs-lookup"><span data-stu-id="41164-312">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="41164-313">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="41164-313">**wwwroot**</span></span>
  * <span data-ttu-id="41164-314">**Css**</span><span class="sxs-lookup"><span data-stu-id="41164-314">**css**</span></span>
  * <span data-ttu-id="41164-315">**images**</span><span class="sxs-lookup"><span data-stu-id="41164-315">**images**</span></span>
  * <span data-ttu-id="41164-316">**Js**</span><span class="sxs-lookup"><span data-stu-id="41164-316">**js**</span></span>
* <span data-ttu-id="41164-317">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="41164-317">**MyStaticFiles**</span></span>
  * <span data-ttu-id="41164-318">**images**</span><span class="sxs-lookup"><span data-stu-id="41164-318">**images**</span></span>
    * <span data-ttu-id="41164-319">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="41164-319">*banner1.svg*</span></span>
  * <span data-ttu-id="41164-320">*default.html*</span><span class="sxs-lookup"><span data-stu-id="41164-320">*default.html*</span></span>

<span data-ttu-id="41164-321">下列程式碼可啟用靜態檔案、預設檔案和 `MyStaticFiles` 的目錄瀏覽功能：</span><span class="sxs-lookup"><span data-stu-id="41164-321">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="41164-322">當 `EnableDirectoryBrowsing` 屬性值是 `true` 時，必須呼叫 `AddDirectoryBrowser`：</span><span class="sxs-lookup"><span data-stu-id="41164-322">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="41164-323">使用檔案階層和上述程式碼時，URL 解析方式如下：</span><span class="sxs-lookup"><span data-stu-id="41164-323">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="41164-324">URI</span><span class="sxs-lookup"><span data-stu-id="41164-324">URI</span></span>            |                             <span data-ttu-id="41164-325">回應</span><span class="sxs-lookup"><span data-stu-id="41164-325">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="41164-326">*HTTP:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="41164-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="41164-327">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="41164-327">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="41164-328">*HTTP:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="41164-328">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="41164-329">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="41164-329">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="41164-330">如果 *>mystaticfiles* 目錄中沒有預設名稱的檔案， *HTTP:// \<server_address> /StaticFiles* 會傳回具有可點按連結的目錄清單：</span><span class="sxs-lookup"><span data-stu-id="41164-330">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![靜態檔案清單](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="41164-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 會從 `http://{SERVER ADDRESS}/StaticFiles` (不含尾端斜線) 執行用戶端重新導向至 `http://{SERVER ADDRESS}/StaticFiles/` (含尾端斜線)。</span><span class="sxs-lookup"><span data-stu-id="41164-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="41164-333">*StaticFiles* 目錄內的相對 URL 若未含尾端斜線則會被視為無效。</span><span class="sxs-lookup"><span data-stu-id="41164-333">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="41164-334">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="41164-334">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="41164-335"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>類別包含一個屬性，此屬性會當做 `Mappings` 副檔名與 MIME 內容類型的對應。</span><span class="sxs-lookup"><span data-stu-id="41164-335">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="41164-336">在下列範例中，已有數個副檔名註冊為已知的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="41164-336">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="41164-337">已取代 *.rtf* 副檔名，並已移除 *.mp4*。</span><span class="sxs-lookup"><span data-stu-id="41164-337">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="41164-338">請參閱 [MIME 內容類型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="41164-338">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="41164-339">如需在 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 伺服器應用程式中使用自訂或設定其他的詳細資訊 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> Blazor ，請參閱 <xref:blazor/fundamentals/static-files> 。</span><span class="sxs-lookup"><span data-stu-id="41164-339">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="41164-340">非標準的內容類型</span><span class="sxs-lookup"><span data-stu-id="41164-340">Non-standard content types</span></span>

<span data-ttu-id="41164-341">靜態檔案中介軟體可理解幾乎 400 種已知的檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="41164-341">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="41164-342">如果使用者要求具有未知檔案類型的檔案，則靜態檔案中介軟體會將該要求傳遞至管線中的下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="41164-342">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="41164-343">如果沒有中介軟體處理要求，則會傳回「404 找不到」回應。</span><span class="sxs-lookup"><span data-stu-id="41164-343">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="41164-344">如果啟用目錄瀏覽功能，則會在目錄清單中顯示檔案的連結。</span><span class="sxs-lookup"><span data-stu-id="41164-344">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="41164-345">下列程式碼可讓您提供未知的類型，並會將未知的檔案轉譯為影像：</span><span class="sxs-lookup"><span data-stu-id="41164-345">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="41164-346">使用上述程式碼時，系統會以影像來回應具有未知內容類型的檔案要求。</span><span class="sxs-lookup"><span data-stu-id="41164-346">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="41164-347">啟用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="41164-347">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="41164-348">預設為停用，亦不建議您使用。</span><span class="sxs-lookup"><span data-stu-id="41164-348">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="41164-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 可提供更安全的替代方法，來提供非標準副檔名的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="41164-350">提供多個位置的檔案</span><span class="sxs-lookup"><span data-stu-id="41164-350">Serve files from multiple locations</span></span>

<span data-ttu-id="41164-351">`UseStaticFiles` 而且 `UseFileServer` 預設為指向 *wwwroot* 的檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="41164-351">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="41164-352">您可以提供其他的實例 `UseStaticFiles` 和 `UseFileServer` 其他檔案提供者，以提供來自其他位置的檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-352">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="41164-353">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="41164-353">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="41164-354">考量</span><span class="sxs-lookup"><span data-stu-id="41164-354">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="41164-355">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能會導致洩漏祕密。</span><span class="sxs-lookup"><span data-stu-id="41164-355">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="41164-356">強烈建議您在生產環境中停用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="41164-356">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="41164-357">透過 `UseStaticFiles` 或 `UseDirectoryBrowser`，仔細檢閱要啟用哪些目錄。</span><span class="sxs-lookup"><span data-stu-id="41164-357">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="41164-358">因為整個目錄和其子目錄都可供公開存取。</span><span class="sxs-lookup"><span data-stu-id="41164-358">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="41164-359">儲存適合在專用目錄中提供公用的檔案，例如 *\<content_root> /wwwroot*。</span><span class="sxs-lookup"><span data-stu-id="41164-359">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="41164-360">將這些檔案和 MVC 視圖分開、 Razor 頁面 (2.x 僅) 、設定檔等。</span><span class="sxs-lookup"><span data-stu-id="41164-360">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="41164-361">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公開內容的 URL 可能有區分大小寫，並受限於基礎檔案系統的字元限制。</span><span class="sxs-lookup"><span data-stu-id="41164-361">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="41164-362">例如，Windows 不區分大小寫&mdash;macOS 和 Linux 則區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="41164-362">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="41164-363">裝載於 IIS 中的 ASP.NET Core 應用程式會使用 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)，將所有要求轉送給應用程式 (包括靜態檔案要求)，</span><span class="sxs-lookup"><span data-stu-id="41164-363">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="41164-364">而不會使用 IIS 靜態檔案處理常式。</span><span class="sxs-lookup"><span data-stu-id="41164-364">The IIS static file handler isn't used.</span></span> <span data-ttu-id="41164-365">因此，上述處理常式不可能在模組處理要求之前處理要求。</span><span class="sxs-lookup"><span data-stu-id="41164-365">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="41164-366">請完成 IIS 管理員中的下列步驟，以移除伺服器或網站層級的 IIS 靜態檔案處理常式：</span><span class="sxs-lookup"><span data-stu-id="41164-366">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="41164-367">巡覽至 [模組] 功能。</span><span class="sxs-lookup"><span data-stu-id="41164-367">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="41164-368">選取清單中的 **StaticFileModule**。</span><span class="sxs-lookup"><span data-stu-id="41164-368">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="41164-369">按一下 [動作] 側邊欄中的 [移除]。</span><span class="sxs-lookup"><span data-stu-id="41164-369">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="41164-370">如果已啟用 IIS 靜態檔案處理常式，**但是** 未正確設定 ASP.NET Core 模組，仍可提供靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="41164-370">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="41164-371">舉例來說，未部署 *web.config* 檔案時可能會發生上述情況。</span><span class="sxs-lookup"><span data-stu-id="41164-371">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="41164-372">將程式碼檔案放在應用程式專案的 [web 根目錄](xref:fundamentals/index#web-root)外 (，包括 *.cs* 和 *cshtml*) 。</span><span class="sxs-lookup"><span data-stu-id="41164-372">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="41164-373">如此一來，即會建立應用程式的用戶端內容與伺服器端程式碼之間的邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="41164-373">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="41164-374">這樣可以防止伺服器端程式碼外洩。</span><span class="sxs-lookup"><span data-stu-id="41164-374">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41164-375">其他資源</span><span class="sxs-lookup"><span data-stu-id="41164-375">Additional resources</span></span>

* [<span data-ttu-id="41164-376">中介軟體</span><span class="sxs-lookup"><span data-stu-id="41164-376">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="41164-377">ASP.NET Core 簡介</span><span class="sxs-lookup"><span data-stu-id="41164-377">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
