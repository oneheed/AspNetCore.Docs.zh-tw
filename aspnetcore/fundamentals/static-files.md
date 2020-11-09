---
title: ASP.NET Core 中的靜態檔案
author: rick-anderson
description: 了解如何在 ASP.NET Core Web 應用程式中提供靜態檔案、保護其安全，並設定靜態檔案裝載中介軟體的行為。
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057852"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="ec8af-103">ASP.NET Core 中的靜態檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec8af-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ec8af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="ec8af-105">靜態檔案（例如 HTML、CSS、影像和 JavaScript）是 ASP.NET Core 應用程式預設會直接提供給用戶端的資產。</span><span class="sxs-lookup"><span data-stu-id="ec8af-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="ec8af-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ec8af-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="ec8af-107">提供靜態檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-107">Serve static files</span></span>

<span data-ttu-id="ec8af-108">靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。</span><span class="sxs-lookup"><span data-stu-id="ec8af-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="ec8af-109">預設目錄是 `{content root}/wwwroot` ，但您可以使用方法來變更它 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="ec8af-110">如需詳細資訊，請參閱 [內容根目錄](xref:fundamentals/index#content-root) 和 [Web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="ec8af-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 方法可將內容根目錄設定為目前的目錄：</span><span class="sxs-lookup"><span data-stu-id="ec8af-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="ec8af-112">上述程式碼是使用 web 應用程式範本所建立。</span><span class="sxs-lookup"><span data-stu-id="ec8af-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="ec8af-113">靜態檔案可透過相對於 [web 根目錄](xref:fundamentals/index#web-root)的路徑來存取。</span><span class="sxs-lookup"><span data-stu-id="ec8af-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="ec8af-114">例如， **Web 應用程式** 專案範本包含資料夾中的數個資料夾 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="ec8af-115">請考慮建立 *wwwroot/images* 資料夾，並新增 *wwwroot/images/MyImage.jpg* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="ec8af-116">用來存取資料夾中檔案的 URI 格式 `images` 為 `https://<hostname>/images/<image_file_name>` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="ec8af-117">例如， `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="ec8af-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="ec8af-118">在 web 根目錄中提供檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-118">Serve files in web root</span></span>

<span data-ttu-id="ec8af-119">預設的 web 應用程式範本 <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> 會在中呼叫方法 `Startup.Configure` ，以啟用靜態檔案的服務：</span><span class="sxs-lookup"><span data-stu-id="ec8af-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="ec8af-120">無參數方法多載會 `UseStaticFiles` 將 [web 根目錄](xref:fundamentals/index#web-root) 中的檔案標示為 servable。</span><span class="sxs-lookup"><span data-stu-id="ec8af-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="ec8af-121">下列標記會參考 *wwwroot/images/MyImage.jpg* ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-121">The following markup references *wwwroot/images/MyImage.jpg* :</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="ec8af-122">在上述程式碼中，波形字元會 `~/` 指向 [web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="ec8af-123">提供 Web 根目錄外的檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-123">Serve files outside of web root</span></span>

<span data-ttu-id="ec8af-124">請考慮要提供的靜態檔案位於 [web 根目錄](xref:fundamentals/index#web-root)外部的目錄階層：</span><span class="sxs-lookup"><span data-stu-id="ec8af-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="ec8af-125">要求可以藉由設定靜態檔案中介軟體來存取檔案，如下所示 `red-rose.jpg` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="ec8af-126">上述程式碼會透過 *StaticFiles* URI 區段公開 *MyStaticFiles* 目錄階層。</span><span class="sxs-lookup"><span data-stu-id="ec8af-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="ec8af-127">`https://<hostname>/StaticFiles/images/red-rose.jpg`服務 *red-rose.jpg* 檔案的要求。</span><span class="sxs-lookup"><span data-stu-id="ec8af-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="ec8af-128">下列標記參考 *>mystaticfiles/images/red-rose.jpg* ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-128">The following markup references *MyStaticFiles/images/red-rose.jpg* :</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="ec8af-129">設定 HTTP 回應標頭</span><span class="sxs-lookup"><span data-stu-id="ec8af-129">Set HTTP response headers</span></span>

<span data-ttu-id="ec8af-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>物件可以用來設定 HTTP 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="ec8af-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="ec8af-131">除了從 [web 根目錄](xref:fundamentals/index#web-root)設定靜態檔案服務之外，下列程式碼還會設定 `Cache-Control` 標頭：</span><span class="sxs-lookup"><span data-stu-id="ec8af-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="ec8af-132">靜態檔案可公開快取600秒：</span><span class="sxs-lookup"><span data-stu-id="ec8af-132">Static files are publicly cacheable for 600 seconds:</span></span>

![已新增顯示「快取控制」標頭的回應標頭](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="ec8af-134">靜態檔案授權</span><span class="sxs-lookup"><span data-stu-id="ec8af-134">Static file authorization</span></span>

<span data-ttu-id="ec8af-135">靜態檔案中介軟體不提供授權檢查。</span><span class="sxs-lookup"><span data-stu-id="ec8af-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="ec8af-136">由 it 提供的任何檔案（包括下的檔案） `wwwroot` 都可以公開存取。</span><span class="sxs-lookup"><span data-stu-id="ec8af-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="ec8af-137">若要依據授權來提供檔案：</span><span class="sxs-lookup"><span data-stu-id="ec8af-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="ec8af-138">將它們儲存 `wwwroot` 在預設靜態檔案中介軟體的外部，以及任何可存取的目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="ec8af-139">`UseStaticFiles`在之後呼叫 `UseAuthorization` 並指定路徑：</span><span class="sxs-lookup"><span data-stu-id="ec8af-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="ec8af-140">上述方法需要驗證使用者：</span><span class="sxs-lookup"><span data-stu-id="ec8af-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="ec8af-141">根據授權提供檔案的替代方法：</span><span class="sxs-lookup"><span data-stu-id="ec8af-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="ec8af-142">將它們儲存 `wwwroot` 在靜態檔案中介軟體的外部，以及任何可存取的目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="ec8af-143">透過套用授權的動作方法來提供服務，並傳回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 物件：</span><span class="sxs-lookup"><span data-stu-id="ec8af-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="ec8af-144">瀏覽目錄</span><span class="sxs-lookup"><span data-stu-id="ec8af-144">Directory browsing</span></span>

<span data-ttu-id="ec8af-145">瀏覽目錄允許在指定的目錄中列出目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="ec8af-146">基於安全性考慮，預設會停用瀏覽目錄功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="ec8af-147">如需詳細資訊，請參閱 [考慮](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="ec8af-148">啟用瀏覽目錄：</span><span class="sxs-lookup"><span data-stu-id="ec8af-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="ec8af-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 在中 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="ec8af-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 在中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="ec8af-151">上述程式碼允許使用 URL 流覽 *wwwroot/images* 資料夾的目錄 `https://<hostname>/MyImages` ，其中包含每個檔案和資料夾的連結：</span><span class="sxs-lookup"><span data-stu-id="ec8af-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![目錄瀏覽](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="ec8af-153">提供預設檔</span><span class="sxs-lookup"><span data-stu-id="ec8af-153">Serve default documents</span></span>

<span data-ttu-id="ec8af-154">設定預設頁面可為訪客提供網站上的起點。</span><span class="sxs-lookup"><span data-stu-id="ec8af-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="ec8af-155">若要從沒有完整 URI 的預設頁面提供服務 `wwwroot` ，請呼叫 <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="ec8af-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="ec8af-156">您必須在 `UseStaticFiles` 之前先呼叫 `UseDefaultFiles`，才能提供預設的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="ec8af-157">`UseDefaultFiles` 是不提供檔案的 URL 重寫工具。</span><span class="sxs-lookup"><span data-stu-id="ec8af-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="ec8af-158">使用時 `UseDefaultFiles` ，會要求搜尋中的資料夾 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="ec8af-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="ec8af-159">*default.htm*</span></span>
* <span data-ttu-id="ec8af-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="ec8af-160">*default.html*</span></span>
* <span data-ttu-id="ec8af-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="ec8af-161">*index.htm*</span></span>
* <span data-ttu-id="ec8af-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="ec8af-162">*index.html*</span></span>

<span data-ttu-id="ec8af-163">系統會提供從清單中找到的第一個檔案，就像提出的要求是完整 URI 一樣。</span><span class="sxs-lookup"><span data-stu-id="ec8af-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="ec8af-164">瀏覽器 URL 仍會繼續反應要求的 URI。</span><span class="sxs-lookup"><span data-stu-id="ec8af-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="ec8af-165">下列程式碼會將預設檔案名稱變更為 *mydefault.html* ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-165">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="ec8af-166">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ec8af-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="ec8af-167">預設檔的 UseFileServer</span><span class="sxs-lookup"><span data-stu-id="ec8af-167">UseFileServer for default documents</span></span>

<span data-ttu-id="ec8af-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 結合、和的功能（ `UseStaticFiles` `UseDefaultFiles` 選擇性） `UseDirectoryBrowser` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="ec8af-169">呼叫 `app.UseFileServer` 以啟用靜態檔案和預設檔案的服務。</span><span class="sxs-lookup"><span data-stu-id="ec8af-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="ec8af-170">未啟用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="ec8af-171">下列程式碼顯示 `Startup.Configure` `UseFileServer` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="ec8af-172">下列程式碼可讓您提供靜態檔案、預設檔案和瀏覽目錄：</span><span class="sxs-lookup"><span data-stu-id="ec8af-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="ec8af-173">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ec8af-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="ec8af-174">請考慮下列目錄階層：</span><span class="sxs-lookup"><span data-stu-id="ec8af-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="ec8af-175">下列程式碼可讓您提供靜態檔案、預設檔案，以及的瀏覽目錄 `MyStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="ec8af-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 當 `EnableDirectoryBrowsing` 屬性值為時，必須呼叫 `true` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="ec8af-177">使用檔案階層和上述程式碼時，URL 解析方式如下：</span><span class="sxs-lookup"><span data-stu-id="ec8af-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="ec8af-178">URI</span><span class="sxs-lookup"><span data-stu-id="ec8af-178">URI</span></span>            |      <span data-ttu-id="ec8af-179">回應</span><span class="sxs-lookup"><span data-stu-id="ec8af-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="ec8af-180">*>mystaticfiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="ec8af-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="ec8af-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="ec8af-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="ec8af-182">如果 *>mystaticfiles* 目錄中沒有預設名稱的檔案，則會傳回 `https://<hostname>/StaticFiles` 具有可點選連結的目錄清單：</span><span class="sxs-lookup"><span data-stu-id="ec8af-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![靜態檔案清單](static-files/_static/db2.png)

<span data-ttu-id="ec8af-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 然後 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 從目標 uri 執行用戶端重新導向，不含尾端的 `/`  目標 uri `/` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="ec8af-185">例如，從 `https://<hostname>/StaticFiles` 到 `https://<hostname>/StaticFiles/` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="ec8af-186">*StaticFiles* 目錄內的相對 url 無效，不含尾端斜線 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="ec8af-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="ec8af-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="ec8af-188"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>類別包含 `Mappings` 屬性，可作為 MIME 內容類型的副檔名對應。</span><span class="sxs-lookup"><span data-stu-id="ec8af-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="ec8af-189">在下列範例中，有數個副檔名對應至已知的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="ec8af-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="ec8af-190">會取代 *.rtf* 副檔名，而且會移除 *. （.* ）：</span><span class="sxs-lookup"><span data-stu-id="ec8af-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="ec8af-191">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ec8af-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="ec8af-192">請參閱 [MIME 內容類型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="ec8af-193">非標準的內容類型</span><span class="sxs-lookup"><span data-stu-id="ec8af-193">Non-standard content types</span></span>

<span data-ttu-id="ec8af-194">靜態檔案中介軟體瞭解將近400個已知的檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="ec8af-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="ec8af-195">如果使用者要求具有未知檔案類型的檔案，則靜態檔案中介軟體會將要求傳遞至管線中的下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="ec8af-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="ec8af-196">如果沒有中介軟體處理要求，則會傳回「404 找不到」  回應。</span><span class="sxs-lookup"><span data-stu-id="ec8af-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="ec8af-197">如果啟用目錄瀏覽功能，則會在目錄清單中顯示檔案的連結。</span><span class="sxs-lookup"><span data-stu-id="ec8af-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="ec8af-198">下列程式碼可讓您提供未知的類型，並會將未知的檔案轉譯為影像：</span><span class="sxs-lookup"><span data-stu-id="ec8af-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="ec8af-199">下列程式碼會顯示 `Startup.Configure` 上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="ec8af-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="ec8af-200">使用上述程式碼時，系統會以影像來回應具有未知內容類型的檔案要求。</span><span class="sxs-lookup"><span data-stu-id="ec8af-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="ec8af-201">啟用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ec8af-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="ec8af-202">預設為停用，亦不建議您使用。</span><span class="sxs-lookup"><span data-stu-id="ec8af-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="ec8af-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 可提供更安全的替代方法，來提供非標準副檔名的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="ec8af-204">提供多個位置的檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-204">Serve files from multiple locations</span></span>

<span data-ttu-id="ec8af-205">`UseStaticFiles` 而且 `UseFileServer` 預設為指向的檔案提供者 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="ec8af-206">`UseStaticFiles`和其他檔案提供者可以提供和的其他實例， `UseFileServer` 以提供來自其他位置的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="ec8af-207">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="ec8af-208">靜態檔案的安全性考慮</span><span class="sxs-lookup"><span data-stu-id="ec8af-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="ec8af-209">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能會導致洩漏祕密。</span><span class="sxs-lookup"><span data-stu-id="ec8af-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="ec8af-210">強烈建議您在生產環境中停用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="ec8af-211">透過 `UseStaticFiles` 或 `UseDirectoryBrowser`，仔細檢閱要啟用哪些目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="ec8af-212">因為整個目錄和其子目錄都可供公開存取。</span><span class="sxs-lookup"><span data-stu-id="ec8af-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="ec8af-213">儲存適合在專用目錄中提供公用的檔案，例如 `<content_root>/wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="ec8af-214">將這些檔案與 MVC 視圖、 Razor 頁面、設定檔等區隔開。</span><span class="sxs-lookup"><span data-stu-id="ec8af-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="ec8af-215">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公開內容的 URL 可能有區分大小寫，並受限於基礎檔案系統的字元限制。</span><span class="sxs-lookup"><span data-stu-id="ec8af-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="ec8af-216">例如，Windows 不區分大小寫，但 macOS 和 Linux 則不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="ec8af-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="ec8af-217">裝載於 IIS 中的 ASP.NET Core 應用程式會使用 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)，將所有要求轉送給應用程式 (包括靜態檔案要求)，</span><span class="sxs-lookup"><span data-stu-id="ec8af-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="ec8af-218">未使用 IIS 靜態檔案處理常式，且沒有機會處理要求。</span><span class="sxs-lookup"><span data-stu-id="ec8af-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="ec8af-219">請完成 IIS 管理員中的下列步驟，以移除伺服器或網站層級的 IIS 靜態檔案處理常式：</span><span class="sxs-lookup"><span data-stu-id="ec8af-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="ec8af-220">巡覽至 [模組]  功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="ec8af-221">選取清單中的 **StaticFileModule** 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="ec8af-222">按一下 [動作]  側邊欄中的 [移除]  。</span><span class="sxs-lookup"><span data-stu-id="ec8af-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="ec8af-223">如果已啟用 IIS 靜態檔案處理常式， **但是** 未正確設定 ASP.NET Core 模組，仍可提供靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="ec8af-224">舉例來說，未部署 *web.config* 檔案時可能會發生上述情況。</span><span class="sxs-lookup"><span data-stu-id="ec8af-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="ec8af-225">將程式碼檔案（包括 *.cs* 和 *cshtml* ）放在應用程式專案的 [web 根目錄](xref:fundamentals/index#web-root)外部。</span><span class="sxs-lookup"><span data-stu-id="ec8af-225">Place code files, including *.cs* and *.cshtml* , outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="ec8af-226">如此一來，即會建立應用程式的用戶端內容與伺服器端程式碼之間的邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="ec8af-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="ec8af-227">這樣可以防止伺服器端程式碼外洩。</span><span class="sxs-lookup"><span data-stu-id="ec8af-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec8af-228">其他資源</span><span class="sxs-lookup"><span data-stu-id="ec8af-228">Additional resources</span></span>

* [<span data-ttu-id="ec8af-229">中介軟體</span><span class="sxs-lookup"><span data-stu-id="ec8af-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ec8af-230">ASP.NET Core 簡介</span><span class="sxs-lookup"><span data-stu-id="ec8af-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec8af-231">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Scott Addie](https://twitter.com/Scott_Addie) 撰寫</span><span class="sxs-lookup"><span data-stu-id="ec8af-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ec8af-232">HTML、CSS、影像和 JavaScript 這類靜態檔案都是 ASP.NET Core 應用程式直接提供給用戶端的資產。</span><span class="sxs-lookup"><span data-stu-id="ec8af-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="ec8af-233">您需要進行一些設定，才能提供這些檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="ec8af-234">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ec8af-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="ec8af-235">提供靜態檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-235">Serve static files</span></span>

<span data-ttu-id="ec8af-236">靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。</span><span class="sxs-lookup"><span data-stu-id="ec8af-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="ec8af-237">預設目錄是 *{content root}/wwwroot* ，但是可以透過方法來變更 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-237">The default directory is *{content root}/wwwroot* , but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="ec8af-238">如需詳細資訊，請參閱[內容根目錄](xref:fundamentals/index#content-root)和 [Web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="ec8af-239">您必須讓應用程式的 Web 主機記住內容根目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="ec8af-240">`WebHost.CreateDefaultBuilder` 方法可將內容根目錄設定為目前的目錄：</span><span class="sxs-lookup"><span data-stu-id="ec8af-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="ec8af-241">靜態檔案可透過相對於 [web 根目錄](xref:fundamentals/index#web-root)的路徑來存取。</span><span class="sxs-lookup"><span data-stu-id="ec8af-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="ec8af-242">例如， **Web 應用程式** 專案範本包含資料夾中的數個資料夾 `wwwroot` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="ec8af-243">用來存取 *images* 子資料夾中檔案的 URI 格式為 *HTTP:// \<server_address> /images/ \<image_file_name>* 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>* .</span></span> <span data-ttu-id="ec8af-244">例如： *http://localhost:9189/images/banner3.svg* 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-244">For example, *http://localhost:9189/images/banner3.svg* .</span></span>

<span data-ttu-id="ec8af-245">如以 .NET Framework 為目標，請將 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="ec8af-246">如以 .NET Core 為目標，[Microsoft.AspNetCore.All 中繼套件](xref:fundamentals/metapackage-app)會包含此套件。</span><span class="sxs-lookup"><span data-stu-id="ec8af-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="ec8af-247">設定 [中介軟體](xref:fundamentals/middleware/index)，以啟用靜態檔案的服務。</span><span class="sxs-lookup"><span data-stu-id="ec8af-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="ec8af-248">提供 Web 根目錄內的檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-248">Serve files inside of web root</span></span>

<span data-ttu-id="ec8af-249"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>在中叫用方法 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="ec8af-250">無參數方法多載會 `UseStaticFiles` 將 [web 根目錄](xref:fundamentals/index#web-root) 中的檔案標示為 servable。</span><span class="sxs-lookup"><span data-stu-id="ec8af-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="ec8af-251">下列標記參考 *wwwroot/images/banner1.svg* ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-251">The following markup references *wwwroot/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="ec8af-252">在上述程式碼中，波形字元會 `~/` 指向 [web 根目錄](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="ec8af-253">提供 Web 根目錄外的檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-253">Serve files outside of web root</span></span>

<span data-ttu-id="ec8af-254">請考慮要提供的靜態檔案位於 [web 根目錄](xref:fundamentals/index#web-root)外部的目錄階層：</span><span class="sxs-lookup"><span data-stu-id="ec8af-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="ec8af-255">透過設定靜態檔案中介軟體，可讓要求存取 *banner1.svg* 檔案，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ec8af-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="ec8af-256">上述程式碼會透過 *StaticFiles* URI 區段公開 *MyStaticFiles* 目錄階層。</span><span class="sxs-lookup"><span data-stu-id="ec8af-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="ec8af-257">對 *Http:// \<server_address> /StaticFiles/images/banner1.svg* 的要求會提供 *>banner1.svg svg* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="ec8af-258">下列標記參考 *MyStaticFiles/images/banner1.svg* ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-258">The following markup references *MyStaticFiles/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="ec8af-259">設定 HTTP 回應標頭</span><span class="sxs-lookup"><span data-stu-id="ec8af-259">Set HTTP response headers</span></span>

<span data-ttu-id="ec8af-260"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>物件可以用來設定 HTTP 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="ec8af-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="ec8af-261">除了從 [web 根目錄](xref:fundamentals/index#web-root)設定靜態檔案服務之外，下列程式碼還會設定 `Cache-Control` 標頭：</span><span class="sxs-lookup"><span data-stu-id="ec8af-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ec8af-262"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>方法存在於[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)封裝中。</span><span class="sxs-lookup"><span data-stu-id="ec8af-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="ec8af-263">檔案已在開發環境中設為可公開快取 10 分鐘 (600 秒)：</span><span class="sxs-lookup"><span data-stu-id="ec8af-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![已新增顯示「快取控制」標頭的回應標頭](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="ec8af-265">靜態檔案授權</span><span class="sxs-lookup"><span data-stu-id="ec8af-265">Static file authorization</span></span>

<span data-ttu-id="ec8af-266">靜態檔案中介軟體不提供授權檢查。</span><span class="sxs-lookup"><span data-stu-id="ec8af-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="ec8af-267">其提供的所有檔案，包括在 *wwwroot* 下的檔案，皆可公開存取。</span><span class="sxs-lookup"><span data-stu-id="ec8af-267">Any files served by it, including those under *wwwroot* , are publicly accessible.</span></span> <span data-ttu-id="ec8af-268">若要依據授權來提供檔案：</span><span class="sxs-lookup"><span data-stu-id="ec8af-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="ec8af-269">請將它們儲存在 *wwwroot* 外部和可存取靜態檔案中介軟體的任何目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="ec8af-270">透過動作方法，將它們提供給已套用授權的目標。</span><span class="sxs-lookup"><span data-stu-id="ec8af-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="ec8af-271">傳回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 物件：</span><span class="sxs-lookup"><span data-stu-id="ec8af-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="ec8af-272">啟用目錄瀏覽</span><span class="sxs-lookup"><span data-stu-id="ec8af-272">Enable directory browsing</span></span>

<span data-ttu-id="ec8af-273">您的 Web 應用程式使用者可藉由目錄瀏覽功能，查看目錄清單及指定目錄內的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="ec8af-274">基於安全性考量，預設為停用目錄瀏覽功能 (請參閱[考量](#considerations))。</span><span class="sxs-lookup"><span data-stu-id="ec8af-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="ec8af-275">藉由叫用中的方法來啟用瀏覽目錄 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="ec8af-276">從下列方法叫用方法，以新增必要的服務 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="ec8af-277">上述程式碼允許使用 URL *Http:// \<server_address> /MyImages* 來流覽 *wwwroot/images* 資料夾，其中包含每個檔案和資料夾的連結：</span><span class="sxs-lookup"><span data-stu-id="ec8af-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* , with links to each file and folder:</span></span>

![目錄瀏覽](static-files/_static/dir-browse.png)

<span data-ttu-id="ec8af-279">如需了解啟用瀏覽功能時的安全性風險，請參閱[考量](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="ec8af-280">請注意下列範例中的兩個 `UseStaticFiles` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="ec8af-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="ec8af-281">第一個呼叫可提供 *wwwroot* 資料夾中的靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="ec8af-282">第二次呼叫會使用 URL *Http:// \<server_address> /MyImages* 來啟用 *wwwroot/images* 資料夾的瀏覽目錄：</span><span class="sxs-lookup"><span data-stu-id="ec8af-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="ec8af-283">提供預設文件</span><span class="sxs-lookup"><span data-stu-id="ec8af-283">Serve a default document</span></span>

<span data-ttu-id="ec8af-284">設定預設的歡迎頁面，可讓訪客在瀏覽您的網站時有個合乎邏輯的起點。</span><span class="sxs-lookup"><span data-stu-id="ec8af-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="ec8af-285">若要在沒有使用者完整限定 URI 的情況下提供預設頁面，請 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 從呼叫方法 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="ec8af-286">您必須在 `UseStaticFiles` 之前先呼叫 `UseDefaultFiles`，才能提供預設的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="ec8af-287">`UseDefaultFiles` 是 URL 重寫器，並非實際提供檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="ec8af-288">透過 `UseStaticFiles` 啟用靜態檔案中介軟體以提供檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="ec8af-289">使用 `UseDefaultFiles` 要求以搜尋資料夾：</span><span class="sxs-lookup"><span data-stu-id="ec8af-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="ec8af-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="ec8af-290">*default.htm*</span></span>
* <span data-ttu-id="ec8af-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="ec8af-291">*default.html*</span></span>
* <span data-ttu-id="ec8af-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="ec8af-292">*index.htm*</span></span>
* <span data-ttu-id="ec8af-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="ec8af-293">*index.html*</span></span>

<span data-ttu-id="ec8af-294">系統會提供從清單中找到的第一個檔案，就像提出的要求是完整 URI 一樣。</span><span class="sxs-lookup"><span data-stu-id="ec8af-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="ec8af-295">瀏覽器 URL 仍會繼續反應要求的 URI。</span><span class="sxs-lookup"><span data-stu-id="ec8af-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="ec8af-296">下列程式碼會將預設檔案名稱變更為 *mydefault.html* ：</span><span class="sxs-lookup"><span data-stu-id="ec8af-296">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="ec8af-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="ec8af-297">UseFileServer</span></span>

<span data-ttu-id="ec8af-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 結合、和的功能（ `UseStaticFiles` `UseDefaultFiles` 選擇性） `UseDirectoryBrowser` 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="ec8af-299">下列程式碼可提供靜態檔案和預設檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="ec8af-300">未啟用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="ec8af-301">下列程式碼會啟用目錄瀏覽功能，以在無參數多載上進行建置：</span><span class="sxs-lookup"><span data-stu-id="ec8af-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="ec8af-302">請考慮下列目錄階層：</span><span class="sxs-lookup"><span data-stu-id="ec8af-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="ec8af-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="ec8af-303">**wwwroot**</span></span>
  * <span data-ttu-id="ec8af-304">**Css**</span><span class="sxs-lookup"><span data-stu-id="ec8af-304">**css**</span></span>
  * <span data-ttu-id="ec8af-305">**images**</span><span class="sxs-lookup"><span data-stu-id="ec8af-305">**images**</span></span>
  * <span data-ttu-id="ec8af-306">**Js**</span><span class="sxs-lookup"><span data-stu-id="ec8af-306">**js**</span></span>
* <span data-ttu-id="ec8af-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="ec8af-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="ec8af-308">**images**</span><span class="sxs-lookup"><span data-stu-id="ec8af-308">**images**</span></span>
    * <span data-ttu-id="ec8af-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="ec8af-309">*banner1.svg*</span></span>
  * <span data-ttu-id="ec8af-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="ec8af-310">*default.html*</span></span>

<span data-ttu-id="ec8af-311">下列程式碼可啟用靜態檔案、預設檔案和 `MyStaticFiles` 的目錄瀏覽功能：</span><span class="sxs-lookup"><span data-stu-id="ec8af-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="ec8af-312">當 `EnableDirectoryBrowsing` 屬性值是 `true` 時，必須呼叫 `AddDirectoryBrowser`：</span><span class="sxs-lookup"><span data-stu-id="ec8af-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="ec8af-313">使用檔案階層和上述程式碼時，URL 解析方式如下：</span><span class="sxs-lookup"><span data-stu-id="ec8af-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="ec8af-314">URI</span><span class="sxs-lookup"><span data-stu-id="ec8af-314">URI</span></span>            |                             <span data-ttu-id="ec8af-315">回應</span><span class="sxs-lookup"><span data-stu-id="ec8af-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="ec8af-316">*HTTP:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="ec8af-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="ec8af-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="ec8af-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="ec8af-318">*HTTP:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="ec8af-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="ec8af-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="ec8af-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="ec8af-320">如果 *>mystaticfiles* 目錄中沒有預設名稱的檔案， *HTTP:// \<server_address> /StaticFiles* 會傳回具有可點按連結的目錄清單：</span><span class="sxs-lookup"><span data-stu-id="ec8af-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![靜態檔案清單](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="ec8af-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 會從 `http://{SERVER ADDRESS}/StaticFiles` (不含尾端斜線) 執行用戶端重新導向至 `http://{SERVER ADDRESS}/StaticFiles/` (含尾端斜線)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="ec8af-323">*StaticFiles* 目錄內的相對 URL 若未含尾端斜線則會被視為無效。</span><span class="sxs-lookup"><span data-stu-id="ec8af-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="ec8af-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="ec8af-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="ec8af-325"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>類別包含一個屬性，此屬性會當做 `Mappings` 副檔名與 MIME 內容類型的對應。</span><span class="sxs-lookup"><span data-stu-id="ec8af-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="ec8af-326">在下列範例中，已有數個副檔名註冊為已知的 MIME 類型。</span><span class="sxs-lookup"><span data-stu-id="ec8af-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="ec8af-327">已取代 *.rtf* 副檔名，並已移除 *.mp4* 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="ec8af-328">請參閱 [MIME 內容類型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="ec8af-329">如需在 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 伺服器應用程式中使用自訂或設定其他的詳細資訊 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> Blazor ，請參閱 <xref:blazor/fundamentals/additional-scenarios#static-files> 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="ec8af-330">非標準的內容類型</span><span class="sxs-lookup"><span data-stu-id="ec8af-330">Non-standard content types</span></span>

<span data-ttu-id="ec8af-331">靜態檔案中介軟體可理解幾乎 400 種已知的檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="ec8af-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="ec8af-332">如果使用者要求具有未知檔案類型的檔案，則靜態檔案中介軟體會將該要求傳遞至管線中的下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="ec8af-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="ec8af-333">如果沒有中介軟體處理要求，則會傳回「404 找不到」  回應。</span><span class="sxs-lookup"><span data-stu-id="ec8af-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="ec8af-334">如果啟用目錄瀏覽功能，則會在目錄清單中顯示檔案的連結。</span><span class="sxs-lookup"><span data-stu-id="ec8af-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="ec8af-335">下列程式碼可讓您提供未知的類型，並會將未知的檔案轉譯為影像：</span><span class="sxs-lookup"><span data-stu-id="ec8af-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="ec8af-336">使用上述程式碼時，系統會以影像來回應具有未知內容類型的檔案要求。</span><span class="sxs-lookup"><span data-stu-id="ec8af-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="ec8af-337">啟用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ec8af-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="ec8af-338">預設為停用，亦不建議您使用。</span><span class="sxs-lookup"><span data-stu-id="ec8af-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="ec8af-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 可提供更安全的替代方法，來提供非標準副檔名的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="ec8af-340">提供多個位置的檔案</span><span class="sxs-lookup"><span data-stu-id="ec8af-340">Serve files from multiple locations</span></span>

<span data-ttu-id="ec8af-341">`UseStaticFiles` 而且 `UseFileServer` 預設為指向 *wwwroot* 的檔案提供者。</span><span class="sxs-lookup"><span data-stu-id="ec8af-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot* .</span></span> <span data-ttu-id="ec8af-342">您可以提供其他的實例 `UseStaticFiles` 和 `UseFileServer` 其他檔案提供者，以提供來自其他位置的檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="ec8af-343">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="ec8af-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="ec8af-344">考量</span><span class="sxs-lookup"><span data-stu-id="ec8af-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="ec8af-345">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能會導致洩漏祕密。</span><span class="sxs-lookup"><span data-stu-id="ec8af-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="ec8af-346">強烈建議您在生產環境中停用目錄瀏覽功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="ec8af-347">透過 `UseStaticFiles` 或 `UseDirectoryBrowser`，仔細檢閱要啟用哪些目錄。</span><span class="sxs-lookup"><span data-stu-id="ec8af-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="ec8af-348">因為整個目錄和其子目錄都可供公開存取。</span><span class="sxs-lookup"><span data-stu-id="ec8af-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="ec8af-349">儲存適合在專用目錄中提供公用的檔案，例如 *\<content_root> /wwwroot* 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot* .</span></span> <span data-ttu-id="ec8af-350">將這些檔案和 MVC 視圖分開、 Razor 頁面 (2.x 僅) 、設定檔等。</span><span class="sxs-lookup"><span data-stu-id="ec8af-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="ec8af-351">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公開內容的 URL 可能有區分大小寫，並受限於基礎檔案系統的字元限制。</span><span class="sxs-lookup"><span data-stu-id="ec8af-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="ec8af-352">例如，Windows 不區分大小寫&mdash;macOS 和 Linux 則區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="ec8af-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="ec8af-353">裝載於 IIS 中的 ASP.NET Core 應用程式會使用 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)，將所有要求轉送給應用程式 (包括靜態檔案要求)，</span><span class="sxs-lookup"><span data-stu-id="ec8af-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="ec8af-354">而不會使用 IIS 靜態檔案處理常式。</span><span class="sxs-lookup"><span data-stu-id="ec8af-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="ec8af-355">因此，上述處理常式不可能在模組處理要求之前處理要求。</span><span class="sxs-lookup"><span data-stu-id="ec8af-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="ec8af-356">請完成 IIS 管理員中的下列步驟，以移除伺服器或網站層級的 IIS 靜態檔案處理常式：</span><span class="sxs-lookup"><span data-stu-id="ec8af-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="ec8af-357">巡覽至 [模組]  功能。</span><span class="sxs-lookup"><span data-stu-id="ec8af-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="ec8af-358">選取清單中的 **StaticFileModule** 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="ec8af-359">按一下 [動作]  側邊欄中的 [移除]  。</span><span class="sxs-lookup"><span data-stu-id="ec8af-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="ec8af-360">如果已啟用 IIS 靜態檔案處理常式， **但是** 未正確設定 ASP.NET Core 模組，仍可提供靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="ec8af-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="ec8af-361">舉例來說，未部署 *web.config* 檔案時可能會發生上述情況。</span><span class="sxs-lookup"><span data-stu-id="ec8af-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="ec8af-362">將程式碼檔案放在應用程式專案的 [web 根目錄](xref:fundamentals/index#web-root)外 (，包括 *.cs* 和 *cshtml* ) 。</span><span class="sxs-lookup"><span data-stu-id="ec8af-362">Place code files (including *.cs* and *.cshtml* ) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="ec8af-363">如此一來，即會建立應用程式的用戶端內容與伺服器端程式碼之間的邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="ec8af-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="ec8af-364">這樣可以防止伺服器端程式碼外洩。</span><span class="sxs-lookup"><span data-stu-id="ec8af-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec8af-365">其他資源</span><span class="sxs-lookup"><span data-stu-id="ec8af-365">Additional resources</span></span>

* [<span data-ttu-id="ec8af-366">中介軟體</span><span class="sxs-lookup"><span data-stu-id="ec8af-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ec8af-367">ASP.NET Core 簡介</span><span class="sxs-lookup"><span data-stu-id="ec8af-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
