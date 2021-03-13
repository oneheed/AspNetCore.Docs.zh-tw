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
# <a name="static-files-in-aspnet-core"></a>ASP.NET Core 中的靜態檔案

::: moniker range=">= aspnetcore-3.0"

由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)

靜態檔案（例如 HTML、CSS、影像和 JavaScript）是 ASP.NET Core 應用程式預設會直接提供給用戶端的資產。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="serve-static-files"></a>提供靜態檔案

靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。 預設目錄是 `{content root}/wwwroot` ，但您可以使用方法來變更它 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 。 如需詳細資訊，請參閱 [內容根目錄](xref:fundamentals/index#content-root) 和 [Web 根目錄](xref:fundamentals/index#web-root)。

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 方法可將內容根目錄設定為目前的目錄：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

上述程式碼是使用 web 應用程式範本所建立。

靜態檔案可透過相對於 [web 根目錄](xref:fundamentals/index#web-root)的路徑來存取。 例如， **Web 應用程式** 專案範本包含資料夾中的數個資料夾 `wwwroot` ：

* `wwwroot`
  * `css`
  * `js`
  * `lib`

請考慮建立 *wwwroot/images* 資料夾，並新增 *wwwroot/images/MyImage.jpg* 檔案。 用來存取資料夾中檔案的 URI 格式 `images` 為 `https://<hostname>/images/<image_file_name>` 。 例如， `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>在 web 根目錄中提供檔案

預設的 web 應用程式範本 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 會在中呼叫方法 `Startup.Configure` ，以啟用靜態檔案的服務：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

無參數方法多載會 `UseStaticFiles` 將 [web 根目錄](xref:fundamentals/index#web-root) 中的檔案標示為 servable。 下列標記會參考 *wwwroot/images/MyImage.jpg*：

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

在上述程式碼中，波形字元會 `~/` 指向 [web 根目錄](xref:fundamentals/index#web-root)。

### <a name="serve-files-outside-of-web-root"></a>提供 Web 根目錄外的檔案

請考慮要提供的靜態檔案位於 [web 根目錄](xref:fundamentals/index#web-root)外部的目錄階層：

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

要求可以藉由設定靜態檔案中介軟體來存取檔案，如下所示 `red-rose.jpg` ：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

上述程式碼會透過 *StaticFiles* URI 區段公開 *MyStaticFiles* 目錄階層。 `https://<hostname>/StaticFiles/images/red-rose.jpg`服務 *red-rose.jpg* 檔案的要求。

下列標記參考 *>mystaticfiles/images/red-rose.jpg*：

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>設定 HTTP 回應標頭

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>物件可以用來設定 HTTP 回應標頭。 除了從 [web 根目錄](xref:fundamentals/index#web-root)設定靜態檔案服務之外，下列程式碼還會設定 `Cache-Control` 標頭：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

靜態檔案可公開快取600秒：

![已新增顯示「快取控制」標頭的回應標頭](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>靜態檔案授權

ASP.NET 核心範本會 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 在呼叫之前呼叫 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 。 大部分的應用程式會遵循此模式。 在授權中介軟體之前呼叫靜態檔案中介軟體：

  * 靜態檔案不會執行任何授權檢查。
  * 靜態檔案中介軟體所提供的靜態檔案（例如這些檔案中介軟體）可 `wwwroot` 公開存取。
  
若要根據授權提供靜態檔案：

  * 將它們儲存在之外 `wwwroot` 。
  * 呼叫 `UseStaticFiles` 之後，指定路徑 `UseAuthorization` 。
  * 設定 [fallback 授權原則](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  在上述程式碼中，fallback 授權原則會要求 ***所有*** 使用者進行驗證。 指定自己的授權需求的端點（例如控制器、頁面等） Razor 不會使用 fallback 授權原則。 例如，使用 Razor 或的頁面、控制器或動作方法，會使用已套用 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 的授權屬性，而非 fallback 授權原則。

  <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 加入 <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 目前的實例，這會強制驗證目前的使用者。

  下的靜態資產可 `wwwroot` 公開存取，因為預設靜態檔案中介軟體 (`app.UseStaticFiles();`) 會先呼叫 `UseAuthentication` 。 *>mystaticfiles* 資料夾中的靜態資產需要驗證。 [範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples)會示範這一點。

根據授權提供檔案的替代方法是：

  * 將它們儲存 `wwwroot` 在靜態檔案中介軟體的外部，以及任何可存取的目錄。
  * 透過套用授權的動作方法來提供服務，並傳回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 物件：

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>瀏覽目錄

瀏覽目錄允許在指定的目錄中列出目錄。

基於安全性考慮，預設會停用瀏覽目錄功能。 如需詳細資訊，請參閱 [考慮](#considerations)。

啟用瀏覽目錄：

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 在中 `Startup.ConfigureServices` 。
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 在中 `Startup.Configure` 。

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

上述程式碼允許使用 URL 流覽 *wwwroot/images* 資料夾的目錄 `https://<hostname>/MyImages` ，其中包含每個檔案和資料夾的連結：

![目錄瀏覽](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>提供預設檔

設定預設頁面可為訪客提供網站上的起點。 若要從沒有完整 URI 的預設頁面提供服務 `wwwroot` ，請呼叫 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

您必須在 `UseStaticFiles` 之前先呼叫 `UseDefaultFiles`，才能提供預設的檔案。 `UseDefaultFiles` 是不提供檔案的 URL 重寫工具。

使用時 `UseDefaultFiles` ，會要求搜尋中的資料夾 `wwwroot` ：

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

系統會提供從清單中找到的第一個檔案，就像提出的要求是完整 URI 一樣。 瀏覽器 URL 仍會繼續反應要求的 URI。

下列程式碼會將預設檔案名稱變更為 *mydefault.html*：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

下列程式碼會顯示 `Startup.Configure` 上述程式碼：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>預設檔的 UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 結合、和的功能（ `UseStaticFiles` `UseDefaultFiles` 選擇性） `UseDirectoryBrowser` 。

呼叫 `app.UseFileServer` 以啟用靜態檔案和預設檔案的服務。 未啟用目錄瀏覽功能。 下列程式碼顯示 `Startup.Configure` `UseFileServer` ：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

下列程式碼可讓您提供靜態檔案、預設檔案和瀏覽目錄：

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

下列程式碼會顯示 `Startup.Configure` 上述程式碼：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

請考慮下列目錄階層：

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

下列程式碼可讓您提供靜態檔案、預設檔案，以及的瀏覽目錄 `MyStaticFiles` ：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 當 `EnableDirectoryBrowsing` 屬性值為時，必須呼叫 `true` 。

使用檔案階層和上述程式碼時，URL 解析方式如下：

| URI            |      回應  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *>mystaticfiles/images/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

如果 *>mystaticfiles* 目錄中沒有預設名稱的檔案，則會傳回 `https://<hostname>/StaticFiles` 具有可點選連結的目錄清單：

![靜態檔案清單](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 然後 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 從目標 uri 執行用戶端重新導向，不含尾端的 `/`  目標 uri `/` 。 例如，從 `https://<hostname>/StaticFiles` 到 `https://<hostname>/StaticFiles/` 。 *StaticFiles* 目錄內的相對 url 無效，不含尾端斜線 (`/`) 。

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>類別包含 `Mappings` 屬性，可作為 MIME 內容類型的副檔名對應。 在下列範例中，有數個副檔名對應至已知的 MIME 類型。 會取代 *.rtf* 副檔名，而且會移除 *. （.* ）：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

下列程式碼會顯示 `Startup.Configure` 上述程式碼：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

請參閱 [MIME 內容類型](https://www.iana.org/assignments/media-types/media-types.xhtml)。

## <a name="non-standard-content-types"></a>非標準的內容類型

靜態檔案中介軟體瞭解將近400個已知的檔案內容類型。 如果使用者要求具有未知檔案類型的檔案，則靜態檔案中介軟體會將要求傳遞至管線中的下一個中介軟體。 如果沒有中介軟體處理要求，則會傳回「404 找不到」回應。 如果啟用目錄瀏覽功能，則會在目錄清單中顯示檔案的連結。

下列程式碼可讓您提供未知的類型，並會將未知的檔案轉譯為影像：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

下列程式碼會顯示 `Startup.Configure` 上述程式碼：

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

使用上述程式碼時，系統會以影像來回應具有未知內容類型的檔案要求。

> [!WARNING]
> 啟用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 有安全性風險。 預設為停用，亦不建議您使用。 [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 可提供更安全的替代方法，來提供非標準副檔名的檔案。

## <a name="serve-files-from-multiple-locations"></a>提供多個位置的檔案

`UseStaticFiles` 而且 `UseFileServer` 預設為指向的檔案提供者 `wwwroot` 。 `UseStaticFiles`和其他檔案提供者可以提供和的其他實例， `UseFileServer` 以提供來自其他位置的檔案。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>靜態檔案的安全性考慮

> [!WARNING]
> `UseDirectoryBrowser` 和 `UseStaticFiles` 可能會導致洩漏祕密。 強烈建議您在生產環境中停用目錄瀏覽功能。 透過 `UseStaticFiles` 或 `UseDirectoryBrowser`，仔細檢閱要啟用哪些目錄。 因為整個目錄和其子目錄都可供公開存取。 儲存適合在專用目錄中提供公用的檔案，例如 `<content_root>/wwwroot` 。 將這些檔案與 MVC 視圖、 Razor 頁面、設定檔等區隔開。

* 使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公開內容的 URL 可能有區分大小寫，並受限於基礎檔案系統的字元限制。 例如，Windows 不區分大小寫，但 macOS 和 Linux 則不區分大小寫。

* 裝載於 IIS 中的 ASP.NET Core 應用程式會使用 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)，將所有要求轉送給應用程式 (包括靜態檔案要求)， 未使用 IIS 靜態檔案處理常式，且沒有機會處理要求。

* 請完成 IIS 管理員中的下列步驟，以移除伺服器或網站層級的 IIS 靜態檔案處理常式：
    1. 巡覽至 [模組] 功能。
    1. 選取清單中的 **StaticFileModule**。
    1. 按一下 [動作] 側邊欄中的 [移除]。

> [!WARNING]
> 如果已啟用 IIS 靜態檔案處理常式，**但是** 未正確設定 ASP.NET Core 模組，仍可提供靜態檔案。 舉例來說，未部署 *web.config* 檔案時可能會發生上述情況。

* 將程式碼檔案（包括 *.cs* 和 *cshtml*）放在應用程式專案的 [web 根目錄](xref:fundamentals/index#web-root)外部。 如此一來，即會建立應用程式的用戶端內容與伺服器端程式碼之間的邏輯分隔。 這樣可以防止伺服器端程式碼外洩。

## <a name="additional-resources"></a>其他資源

* [中介軟體](xref:fundamentals/middleware/index)
* [ASP.NET Core 簡介](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Scott Addie](https://twitter.com/Scott_Addie) 撰寫

HTML、CSS、影像和 JavaScript 這類靜態檔案都是 ASP.NET Core 應用程式直接提供給用戶端的資產。 您需要進行一些設定，才能提供這些檔案。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="serve-static-files"></a>提供靜態檔案

靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。 預設目錄是 *{content root}/wwwroot*，但是可以透過方法來變更 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 。 如需詳細資訊，請參閱[內容根目錄](xref:fundamentals/index#content-root)和 [Web 根目錄](xref:fundamentals/index#web-root)。

您必須讓應用程式的 Web 主機記住內容根目錄。

`WebHost.CreateDefaultBuilder` 方法可將內容根目錄設定為目前的目錄：

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

靜態檔案可透過相對於 [web 根目錄](xref:fundamentals/index#web-root)的路徑來存取。 例如， **Web 應用程式** 專案範本包含資料夾中的數個資料夾 `wwwroot` ：

* `wwwroot`
  * `css`
  * `images`
  * `js`

用來存取 *images* 子資料夾中檔案的 URI 格式為 *HTTP:// \<server_address> /images/ \<image_file_name>*。 例如： *http://localhost:9189/images/banner3.svg* 。

如以 .NET Framework 為目標，請將 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 套件新增至專案。 如以 .NET Core 為目標，[Microsoft.AspNetCore.All 中繼套件](xref:fundamentals/metapackage-app)會包含此套件。

設定 [中介軟體](xref:fundamentals/middleware/index)，以啟用靜態檔案的服務。

### <a name="serve-files-inside-of-web-root"></a>提供 Web 根目錄內的檔案

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>在中叫用方法 `Startup.Configure` ：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

無參數方法多載會 `UseStaticFiles` 將 [web 根目錄](xref:fundamentals/index#web-root) 中的檔案標示為 servable。 下列標記參考 *wwwroot/images/banner1.svg*：

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

在上述程式碼中，波形字元會 `~/` 指向 [web 根目錄](xref:fundamentals/index#web-root)。

### <a name="serve-files-outside-of-web-root"></a>提供 Web 根目錄外的檔案

請考慮要提供的靜態檔案位於 [web 根目錄](xref:fundamentals/index#web-root)外部的目錄階層：

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

透過設定靜態檔案中介軟體，可讓要求存取 *banner1.svg* 檔案，如下所示：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

上述程式碼會透過 *StaticFiles* URI 區段公開 *MyStaticFiles* 目錄階層。 對 *Http:// \<server_address> /StaticFiles/images/banner1.svg* 的要求會提供 *>banner1.svg svg* 檔案。

下列標記參考 *MyStaticFiles/images/banner1.svg*：

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>設定 HTTP 回應標頭

<xref:Microsoft.AspNetCore.Builder.StaticFileOptions>物件可以用來設定 HTTP 回應標頭。 除了從 [web 根目錄](xref:fundamentals/index#web-root)設定靜態檔案服務之外，下列程式碼還會設定 `Cache-Control` 標頭：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>方法存在於[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)封裝中。

檔案已在開發環境中設為可公開快取 10 分鐘 (600 秒)：

![已新增顯示「快取控制」標頭的回應標頭](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>靜態檔案授權

靜態檔案中介軟體不提供授權檢查。 其提供的所有檔案，包括在 *wwwroot* 下的檔案，皆可公開存取。 若要依據授權來提供檔案：

* 請將它們儲存在 *wwwroot* 外部和可存取靜態檔案中介軟體的任何目錄。
* 透過動作方法，將它們提供給已套用授權的目標。 傳回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 物件：

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>啟用目錄瀏覽

您的 Web 應用程式使用者可藉由目錄瀏覽功能，查看目錄清單及指定目錄內的檔案。 基於安全性考量，預設為停用目錄瀏覽功能 (請參閱[考量](#considerations))。 藉由叫用中的方法來啟用瀏覽目錄 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> `Startup.Configure` ：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

從下列方法叫用方法，以新增必要的服務 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> `Startup.ConfigureServices` ：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

上述程式碼允許使用 URL *Http:// \<server_address> /MyImages* 來流覽 *wwwroot/images* 資料夾，其中包含每個檔案和資料夾的連結：

![目錄瀏覽](static-files/_static/dir-browse.png)

如需了解啟用瀏覽功能時的安全性風險，請參閱[考量](#considerations)。

請注意下列範例中的兩個 `UseStaticFiles` 呼叫。 第一個呼叫可提供 *wwwroot* 資料夾中的靜態檔案。 第二次呼叫會使用 URL *Http:// \<server_address> /MyImages* 來啟用 *wwwroot/images* 資料夾的瀏覽目錄：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>提供預設文件

設定預設的歡迎頁面，可讓訪客在瀏覽您的網站時有個合乎邏輯的起點。 若要在沒有使用者完整限定 URI 的情況下提供預設頁面，請 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 從呼叫方法 `Startup.Configure` ：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> 您必須在 `UseStaticFiles` 之前先呼叫 `UseDefaultFiles`，才能提供預設的檔案。 `UseDefaultFiles` 是 URL 重寫器，並非實際提供檔案。 透過 `UseStaticFiles` 啟用靜態檔案中介軟體以提供檔案。

使用 `UseDefaultFiles` 要求以搜尋資料夾：

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

系統會提供從清單中找到的第一個檔案，就像提出的要求是完整 URI 一樣。 瀏覽器 URL 仍會繼續反應要求的 URI。

下列程式碼會將預設檔案名稱變更為 *mydefault.html*：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 結合、和的功能（ `UseStaticFiles` `UseDefaultFiles` 選擇性） `UseDirectoryBrowser` 。

下列程式碼可提供靜態檔案和預設檔案。 未啟用目錄瀏覽功能。

```csharp
app.UseFileServer();
```

下列程式碼會啟用目錄瀏覽功能，以在無參數多載上進行建置：

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

請考慮下列目錄階層：

* **wwwroot**
  * **Css**
  * **images**
  * **Js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

下列程式碼可啟用靜態檔案、預設檔案和 `MyStaticFiles` 的目錄瀏覽功能：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

當 `EnableDirectoryBrowsing` 屬性值是 `true` 時，必須呼叫 `AddDirectoryBrowser`：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

使用檔案階層和上述程式碼時，URL 解析方式如下：

| URI            |                             回應  |
| ------- | ------|
| *HTTP:// \<server_address> /StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *HTTP:// \<server_address> /StaticFiles*             |     MyStaticFiles/default.html |

如果 *>mystaticfiles* 目錄中沒有預設名稱的檔案， *HTTP:// \<server_address> /StaticFiles* 會傳回具有可點按連結的目錄清單：

![靜態檔案清單](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 會從 `http://{SERVER ADDRESS}/StaticFiles` (不含尾端斜線) 執行用戶端重新導向至 `http://{SERVER ADDRESS}/StaticFiles/` (含尾端斜線)。 *StaticFiles* 目錄內的相對 URL 若未含尾端斜線則會被視為無效。

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>類別包含一個屬性，此屬性會當做 `Mappings` 副檔名與 MIME 內容類型的對應。 在下列範例中，已有數個副檔名註冊為已知的 MIME 類型。 已取代 *.rtf* 副檔名，並已移除 *.mp4*。

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

請參閱 [MIME 內容類型](https://www.iana.org/assignments/media-types/media-types.xhtml)。

如需在 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 伺服器應用程式中使用自訂或設定其他的詳細資訊 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> Blazor ，請參閱 <xref:blazor/fundamentals/static-files> 。

## <a name="non-standard-content-types"></a>非標準的內容類型

靜態檔案中介軟體可理解幾乎 400 種已知的檔案內容類型。 如果使用者要求具有未知檔案類型的檔案，則靜態檔案中介軟體會將該要求傳遞至管線中的下一個中介軟體。 如果沒有中介軟體處理要求，則會傳回「404 找不到」回應。 如果啟用目錄瀏覽功能，則會在目錄清單中顯示檔案的連結。

下列程式碼可讓您提供未知的類型，並會將未知的檔案轉譯為影像：

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

使用上述程式碼時，系統會以影像來回應具有未知內容類型的檔案要求。

> [!WARNING]
> 啟用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 有安全性風險。 預設為停用，亦不建議您使用。 [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 可提供更安全的替代方法，來提供非標準副檔名的檔案。

## <a name="serve-files-from-multiple-locations"></a>提供多個位置的檔案

`UseStaticFiles` 而且 `UseFileServer` 預設為指向 *wwwroot* 的檔案提供者。 您可以提供其他的實例 `UseStaticFiles` 和 `UseFileServer` 其他檔案提供者，以提供來自其他位置的檔案。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。

### <a name="considerations"></a>考量

> [!WARNING]
> `UseDirectoryBrowser` 和 `UseStaticFiles` 可能會導致洩漏祕密。 強烈建議您在生產環境中停用目錄瀏覽功能。 透過 `UseStaticFiles` 或 `UseDirectoryBrowser`，仔細檢閱要啟用哪些目錄。 因為整個目錄和其子目錄都可供公開存取。 儲存適合在專用目錄中提供公用的檔案，例如 *\<content_root> /wwwroot*。 將這些檔案和 MVC 視圖分開、 Razor 頁面 (2.x 僅) 、設定檔等。

* 使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公開內容的 URL 可能有區分大小寫，並受限於基礎檔案系統的字元限制。 例如，Windows 不區分大小寫&mdash;macOS 和 Linux 則區分大小寫。

* 裝載於 IIS 中的 ASP.NET Core 應用程式會使用 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)，將所有要求轉送給應用程式 (包括靜態檔案要求)， 而不會使用 IIS 靜態檔案處理常式。 因此，上述處理常式不可能在模組處理要求之前處理要求。

* 請完成 IIS 管理員中的下列步驟，以移除伺服器或網站層級的 IIS 靜態檔案處理常式：
    1. 巡覽至 [模組] 功能。
    1. 選取清單中的 **StaticFileModule**。
    1. 按一下 [動作] 側邊欄中的 [移除]。

> [!WARNING]
> 如果已啟用 IIS 靜態檔案處理常式，**但是** 未正確設定 ASP.NET Core 模組，仍可提供靜態檔案。 舉例來說，未部署 *web.config* 檔案時可能會發生上述情況。

* 將程式碼檔案放在應用程式專案的 [web 根目錄](xref:fundamentals/index#web-root)外 (，包括 *.cs* 和 *cshtml*) 。 如此一來，即會建立應用程式的用戶端內容與伺服器端程式碼之間的邏輯分隔。 這樣可以防止伺服器端程式碼外洩。

## <a name="additional-resources"></a>其他資源

* [中介軟體](xref:fundamentals/middleware/index)
* [ASP.NET Core 簡介](xref:index)

::: moniker-end
