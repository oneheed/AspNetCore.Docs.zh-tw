---
title: 裝載和部署 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何 Blazor 使用 ASP.NET Core、內容傳遞網路 (CDN) 、檔案伺服器和 GitHub 頁面來裝載和部署應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 53b31bfb5aafb67e8544b146209b221de37c3cc4
ms.sourcegitcommit: 7354c2029164702d075fd3786d96a92c6d49bc6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106164262"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7f6dd-103">裝載和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f6dd-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7f6dd-104">使用[ Blazor WebAssembly 裝載模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-104">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="7f6dd-105">Blazor應用程式、其相依性和 .net 執行時間會以平行方式下載至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-105">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="7f6dd-106">應用程式會直接在瀏覽器 UI 執行緒上執行。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-106">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="7f6dd-107">以下是支援的部署策略：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-107">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="7f6dd-108">Blazor應用程式是由 ASP.NET Core 應用程式提供服務。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-108">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="7f6dd-109">此策略已於[搭配 ASP.NET Core 的已裝載部署](#hosted-deployment-with-aspnet-core)一節中涵蓋。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-109">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="7f6dd-110">Blazor應用程式會放置在靜態裝載 web 伺服器或服務上，而不會使用 .net 來提供 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-110">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="7f6dd-111">此策略包含在 [獨立部署](#standalone-deployment) 區段中，其中包含將 Blazor WebAssembly 應用程式裝載為 IIS 子應用程式的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-111">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="7f6dd-112">壓縮</span><span class="sxs-lookup"><span data-stu-id="7f6dd-112">Compression</span></span>

<span data-ttu-id="7f6dd-113">Blazor WebAssembly發佈應用程式時，會在發行期間以靜態方式壓縮輸出，以減少應用程式的大小，並移除執行時間壓縮的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-113">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="7f6dd-114">使用的壓縮演算法如下：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-114">The following compression algorithms are used:</span></span>

* <span data-ttu-id="7f6dd-115">[Brotli](https://tools.ietf.org/html/rfc7932) (最高層級) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-115">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="7f6dd-116">Gzip</span><span class="sxs-lookup"><span data-stu-id="7f6dd-116">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="7f6dd-117">Blazor 依賴主機提供適當的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-117">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="7f6dd-118">使用 ASP.NET Core 裝載的專案時，主專案可以執行內容協商以及提供靜態壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-118">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="7f6dd-119">裝載 Blazor WebAssembly 獨立應用程式時，可能需要額外的工作，以確保提供靜態壓縮檔案：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-119">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="7f6dd-120">如需 IIS `web.config` 壓縮設定，請參閱 [Iis： Brotli 和 Gzip 壓縮](#brotli-and-gzip-compression) 一節。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-120">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="7f6dd-121">裝載在不支援靜態壓縮的檔案內容協商的靜態裝載方案（例如 GitHub 頁面）時，請考慮將應用程式設定為提取和解碼 Brotli 壓縮檔案：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-121">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="7f6dd-122">從 [google/Brotli GitHub 存放庫](https://github.com/google/brotli)取得 JavaScript Brotli 解碼器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-122">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="7f6dd-123">`decode.js`系統會在存放庫的[ `js` 資料夾](https://github.com/google/brotli/tree/master/js)中命名並找到該解碼器檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-123">The decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="7f6dd-124">縮減版本的 `decode.js` 腳本 (`decode.min.js`) [Google/brotli GitHub 存放庫](https://github.com/google/brotli)中有回歸。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-124">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="7f6dd-125">[在 decode.min.js (google/brotli #881) 的 TypeError](https://github.com/google/brotli/issues/881)問題解決之前，請採取下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-125">Until the issue [TypeError in decode.min.js (google/brotli #881)](https://github.com/google/brotli/issues/881) is resolved, take one of the following approaches:</span></span>
    >
    > * <span data-ttu-id="7f6dd-126">暫時使用腳本的 unminified 版本。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-126">Temporarily use the unminified version of the script.</span></span>
    > * <span data-ttu-id="7f6dd-127">使用與 ASP.NET Core 相容的協力廠商縮制工具，在組建階段自動縮短腳本。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-127">Automatically minify the script at build-time with a third-party minification tool compatible with ASP.NET Core.</span></span>
    > * <span data-ttu-id="7f6dd-128">使用 [npm 套件](https://www.npmjs.com/package/brotli)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-128">Use the [npm package](https://www.npmjs.com/package/brotli).</span></span>
    >
    > <span data-ttu-id="7f6dd-129">本節中的範例程式碼會使用 **unminified** 版本的腳本 (`decode.js`) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-129">The example code in this section uses the **unminified** version of the script (`decode.js`).</span></span>

  * <span data-ttu-id="7f6dd-130">更新應用程式以使用此解碼器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-130">Update the app to use the decoder.</span></span> <span data-ttu-id="7f6dd-131">將結束記號內的標記變更 `<body>` `wwwroot/index.html` 為下列內容：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-131">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="7f6dd-132">若要停用壓縮，請在 `BlazorEnableCompression` 應用程式的專案檔中新增 MSBuild 屬性，並將值設定為 `false` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-132">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="7f6dd-133">您 `BlazorEnableCompression` 可以 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 在命令 shell 中使用下列語法，將屬性傳遞至命令：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-133">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="7f6dd-134">重寫 URL 以便正確地路由</span><span class="sxs-lookup"><span data-stu-id="7f6dd-134">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="7f6dd-135">應用程式中頁面元件的路由要求 Blazor WebAssembly ，與裝載應用程式中的路由要求一樣簡單 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-135">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="7f6dd-136">請考慮 Blazor WebAssembly 具有兩個元件的應用程式：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-136">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="7f6dd-137">`Main.razor`：在應用程式的根目錄載入，並且包含 `About` 元件 () 的連結 `href="About"` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-137">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="7f6dd-138">`About.razor`： `About` component。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-138">`About.razor`: `About` component.</span></span>

<span data-ttu-id="7f6dd-139">使用瀏覽器的網址列要求應用程式預設文件時 (例如 `https://www.contoso.com/`)：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-139">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="7f6dd-140">瀏覽器提出要求。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-140">The browser makes a request.</span></span>
1. <span data-ttu-id="7f6dd-141">預設頁面會傳回，通常是 `index.html` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-141">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="7f6dd-142">`index.html` 啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-142">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="7f6dd-143">Blazor的路由器會載入，而 Razor `Main` 元件則會呈現。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-143">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="7f6dd-144">在主頁面中，選取元件的連結可 `About` 在用戶端上運作，因為 Blazor 路由器會停止瀏覽器在網際網路上提出要求， `www.contoso.com` 並提供轉譯 `About` 的 `About` 元件本身。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-144">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="7f6dd-145">*Blazor WebAssembly 應用程式內* 內部端點的所有要求運作方式相同：要求不會對網際網路上伺服器裝載的資源觸發以瀏覽器為基礎的要求。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-145">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="7f6dd-146">路由器會在內部處理要求。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-146">The router handles the requests internally.</span></span>

<span data-ttu-id="7f6dd-147">如果使用瀏覽器之網址列提出對 `www.contoso.com/About` 的要求，則要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-147">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="7f6dd-148">在應用程式的網際網路主機上沒有這類資源存在，因此會傳回「404 - 找不到」的回應。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-148">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="7f6dd-149">因為瀏覽器會對以網際網路為基礎的主機發出要求，以提供用戶端頁面，所以 web 伺服器和主機服務必須將所有不在伺服器上的資源要求重寫為 `index.html` 頁面。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-149">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="7f6dd-150">當 `index.html` 傳回時，應用程式的 Blazor 路由器會接管並回應正確的資源。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-150">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="7f6dd-151">部署至 IIS 伺服器時，您可以使用 URL 重寫模組與應用程式的已發佈檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-151">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="7f6dd-152">如需詳細資訊，請參閱 [IIS](#iis) 一節。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-152">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="7f6dd-153">搭配 ASP.NET Core 的已裝載部署</span><span class="sxs-lookup"><span data-stu-id="7f6dd-153">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="7f6dd-154">*託管部署* Blazor WebAssembly 可從 web 伺服器上執行的 [ASP.NET Core 應用程式](xref:index)，將應用程式提供給瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-154">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="7f6dd-155">用戶端 Blazor WebAssembly 應用程式會 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 與伺服器應用程式的任何其他靜態 web 資產一起發行至伺服器應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-155">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="7f6dd-156">這兩個應用程式會一起部署。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-156">The two apps are deployed together.</span></span> <span data-ttu-id="7f6dd-157">需要有能夠裝載 ASP.NET Core 應用程式的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-157">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="7f6dd-158">針對裝載的部署，當使用命令) 時，Visual Studio 包含 **Blazor WebAssembly 應用程式** 專案範本 (`blazorwasm` 範本， [`dotnet new`](/dotnet/core/tools/dotnet-new) **`Hosted`** (`-ho|--hosted` 使用命令) 時所選取的選項 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-158">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="7f6dd-159">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-159">For more information, see the following articles:</span></span>

* <span data-ttu-id="7f6dd-160">ASP.NET Core 應用程式裝載和部署： <xref:host-and-deploy/index></span><span class="sxs-lookup"><span data-stu-id="7f6dd-160">ASP.NET Core app hosting and deployment: <xref:host-and-deploy/index></span></span>
* <span data-ttu-id="7f6dd-161">部署至 Azure App Service： <xref:tutorials/publish-to-azure-webapp-using-vs></span><span class="sxs-lookup"><span data-stu-id="7f6dd-161">Deployment to Azure App Service: <xref:tutorials/publish-to-azure-webapp-using-vs></span></span>
* <span data-ttu-id="7f6dd-162">Blazor 專案範本： <xref:blazor/project-structure></span><span class="sxs-lookup"><span data-stu-id="7f6dd-162">Blazor project templates: <xref:blazor/project-structure></span></span>

## <a name="hosted-deployment-with-multiple-blazor-webassembly-apps"></a><span data-ttu-id="7f6dd-163">具有多個應用程式的託管部署 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f6dd-163">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="7f6dd-164">應用程式組態</span><span class="sxs-lookup"><span data-stu-id="7f6dd-164">App configuration</span></span>

<span data-ttu-id="7f6dd-165">託管 Blazor 解決方案可提供多個 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-165">Hosted Blazor solutions can serve multiple Blazor WebAssembly apps.</span></span>

> [!NOTE]
> <span data-ttu-id="7f6dd-166">本章節中的範例會參考 Visual Studio *解決方案* 的使用方式，但不需要使用 Visual Studio 和 Visual Studio 解決方案來處理託管應用程式案例中的多個用戶端應用程式 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-166">The example in this section references the use of a Visual Studio *solution*, but the use of Visual Studio and a Visual Studio solution isn't required for multiple client apps to work in a hosted Blazor WebAssembly apps scenario.</span></span> <span data-ttu-id="7f6dd-167">如果您未使用 Visual Studio，請略過檔案 `{SOLUTION NAME}.sln` 和針對 Visual Studio 所建立的任何其他檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-167">If you aren't using Visual Studio, ignore the `{SOLUTION NAME}.sln` file and any other files created for Visual Studio.</span></span>

<span data-ttu-id="7f6dd-168">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="7f6dd-168">In the following example:</span></span>

* <span data-ttu-id="7f6dd-169">初始 (第一個) 用戶端應用程式是從專案範本建立之方案的預設用戶端專案 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-169">The initial (first) client app is the default client project of a solution created from the Blazor WebAssembly project template.</span></span> <span data-ttu-id="7f6dd-170">第一個用戶端應用程式可以在瀏覽器中從 `/FirstApp` 埠5001或主機上的 URL 存取 `firstapp.com` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-170">The first client app is accessible in a browser from the URL `/FirstApp` on either port 5001 or with a host of `firstapp.com`.</span></span>
* <span data-ttu-id="7f6dd-171">第二個用戶端應用程式會加入至方案中 `SecondBlazorApp.Client` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-171">A second client app is added to the solution, `SecondBlazorApp.Client`.</span></span> <span data-ttu-id="7f6dd-172">第二個用戶端應用程式可以在瀏覽器中從 `/SecondApp` 埠5002或主機上的 URL 存取 `secondapp.com` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-172">The second client app is accessible in a browser from the the URL `/SecondApp` on either port 5002 or with a host of `secondapp.com`.</span></span>

<span data-ttu-id="7f6dd-173">使用現有的主控 Blazor 方案，或從裝載的專案範本建立新的方案 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-173">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template:</span></span>

* <span data-ttu-id="7f6dd-174">在用戶端應用程式的專案檔中，將屬性新增至，並以的 `<StaticWebAssetBasePath>` `<PropertyGroup>` 值 `FirstApp` 設定專案靜態資產的基底路徑：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-174">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="7f6dd-175">將第二個用戶端應用程式新增至解決方案：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-175">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="7f6dd-176">將名為的資料夾加入 `SecondClient` 方案的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-176">Add a folder named `SecondClient` to the solution's folder.</span></span> <span data-ttu-id="7f6dd-177">在新增資料夾後，從專案範本建立的方案資料夾會包含下列方案檔和資料夾 `SecondClient` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-177">The solution folder created from the project template contains the following solution file and folders after the `SecondClient` folder is added:</span></span>
  
    * <span data-ttu-id="7f6dd-178">`Client` (資料夾) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-178">`Client` (folder)</span></span>
    * <span data-ttu-id="7f6dd-179">`SecondClient` (資料夾) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-179">`SecondClient` (folder)</span></span>
    * <span data-ttu-id="7f6dd-180">`Server` (資料夾) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-180">`Server` (folder)</span></span>
    * <span data-ttu-id="7f6dd-181">`Shared` (資料夾) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-181">`Shared` (folder)</span></span>
    * <span data-ttu-id="7f6dd-182">`{SOLUTION NAME}.sln` (檔案) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-182">`{SOLUTION NAME}.sln` (file)</span></span>

    <span data-ttu-id="7f6dd-183">預留位置 `{SOLUTION NAME}` 是解決方案的名稱。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-183">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

  * <span data-ttu-id="7f6dd-184">在 Blazor WebAssembly `SecondBlazorApp.Client` 專案範本的資料夾中，建立名為的應用程式 `SecondClient` Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-184">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>

  * <span data-ttu-id="7f6dd-185">在 `SecondBlazorApp.Client` 應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-185">In the `SecondBlazorApp.Client` app's project file:</span></span>

    * <span data-ttu-id="7f6dd-186">將屬性新增至，其 `<StaticWebAssetBasePath>` 值為 `<PropertyGroup>` `SecondApp` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-186">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="7f6dd-187">將專案參考新增至 `Shared` 專案：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-187">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="7f6dd-188">預留位置 `{SOLUTION NAME}` 是解決方案的名稱。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-188">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="7f6dd-189">在伺服器應用程式的專案檔中，為新增的 `SecondBlazorApp.Client` 用戶端應用程式建立專案參考：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-189">In the server app's project file, create a project reference for the added `SecondBlazorApp.Client` client app:</span></span>

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  <span data-ttu-id="7f6dd-190">預留位置 `{SOLUTION NAME}` 是解決方案的名稱。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-190">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="7f6dd-191">在伺服器應用程式的 `Properties/launchSettings.json` 檔案中，設定 `applicationUrl` Kestrel 設定檔 () 的， `{SOLUTION NAME}.Server` 以存取埠5001和5002上的用戶端應用程式：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-191">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="7f6dd-192">在伺服器應用程式的 `Startup.Configure` 方法 (`Startup.cs`) 中，移除下列幾行，這些行會在呼叫之後出現 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-192">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="7f6dd-193">新增將要求對應至用戶端應用程式的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-193">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="7f6dd-194">下列範例會將中介軟體設定為在下列情況下執行：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-194">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="7f6dd-195">針對已新增的用戶端應用程式，要求埠可能是5001（適用于原始用戶端應用程式）或5002。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-195">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="7f6dd-196">要求主機 `firstapp.com` 適用于原始用戶端應用程式或 `secondapp.com` 新增的用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-196">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="7f6dd-197">本節所示的範例需要下列各項的額外設定：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-197">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="7f6dd-198">存取範例主機網域、和上的應用 `firstapp.com` 程式 `secondapp.com` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-198">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="7f6dd-199">用戶端應用程式用來啟用 TLS 安全性的憑證 (HTTPS) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-199">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="7f6dd-200">必要的設定已超出本文的範圍，並取決於裝載解決方案的方式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-200">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="7f6dd-201">如需詳細資訊，請參閱 [主機和部署文章](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-201">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="7f6dd-202">將下列程式碼放在先前移除的行：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-202">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="7f6dd-203">在伺服器應用程式的氣象預報控制器 (`Controllers/WeatherForecastController.cs`) 中，將現有的路由 (`[Route("[controller]")]`) 取代為 `WeatherForecastController` 下列路由：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-203">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="7f6dd-204">先前新增至伺服器應用程式方法的中介軟體會 `Startup.Configure` `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` 根據埠 (5001/5002) 或網域 (`firstapp.com` / `secondapp.com`) ，將連入要求修改至或。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-204">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="7f6dd-205">需要上述的控制器路由，才能將天氣資料從伺服器應用程式傳回給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-205">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="7f6dd-206">靜態資產和類別庫</span><span class="sxs-lookup"><span data-stu-id="7f6dd-206">Static assets and class libraries</span></span>

<span data-ttu-id="7f6dd-207">針對靜態資產，請使用下列方法：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-207">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="7f6dd-208">當資產位於用戶端應用程式的 `wwwroot` 資料夾時，請正常提供其路徑：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-208">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="7f6dd-209">當資產位於 `wwwroot` 類別庫的資料夾中時[ Razor (RCL) ](xref:blazor/components/class-libraries)，請依[RCL 文章](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)中的指導方針參考用戶端應用程式中的靜態資產：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-209">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="7f6dd-210">類別庫提供給用戶端應用程式的元件會正常地參考。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-210">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="7f6dd-211">如果有任何元件需要樣式表單或 JavaScript 檔案，用戶端應用程式的檔案 `wwwroot/index.html` 必須包含正確的靜態資產連結。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-211">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="7f6dd-212">下列範例會示範這些方法。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-212">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="7f6dd-213">將下列 `Jeep` 元件新增至其中一個用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-213">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="7f6dd-214">`Jeep`元件使用：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-214">The `Jeep` component uses:</span></span>

* <span data-ttu-id="7f6dd-215">從用戶端應用程式的 `wwwroot` 資料夾 () 的映射 `jeep-cj.png` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-215">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="7f6dd-216">新增的元件連結 [ Razor 庫](xref:blazor/components/class-libraries) 中的映射 (`JeepImage`) `wwwroot` 資料夾 (`jeep-yj.png`) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-216">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="7f6dd-217">`Component1`當連結 `JeepImage` 庫加入至方案時，RCL 專案範本會自動建立範例元件 () 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-217">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="7f6dd-218">除非您擁有映射， **否則請勿公開發布車輛** 的影像。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-218">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="7f6dd-219">否則，您會面臨著作權侵權的風險。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-219">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="7f6dd-220">程式庫的 `jeep-yj.png` 映射也可以新增至程式庫的 `Component1` 元件 (`Component1.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-220">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="7f6dd-221">用戶端應用程式的檔案會 `wwwroot/index.html` 以下列新增的標記要求程式庫的樣式表單 `<link>` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-221">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="7f6dd-222">將流覽新增至 `Jeep` 用戶端應用程式元件中的元件 `NavMenu` (`Shared/NavMenu.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-222">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="7f6dd-223">如需 RCLs 的詳細資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-223">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="7f6dd-224">獨立部署</span><span class="sxs-lookup"><span data-stu-id="7f6dd-224">Standalone deployment</span></span>

<span data-ttu-id="7f6dd-225">*獨立部署* Blazor WebAssembly 會以一組直接由用戶端要求的靜態檔案來提供應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-225">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="7f6dd-226">任何靜態檔案伺服器都能提供 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-226">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="7f6dd-227">獨立部署資產會發佈到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-227">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="7f6dd-228">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7f6dd-228">Azure App Service</span></span>

<span data-ttu-id="7f6dd-229">Blazor WebAssembly 您可以將應用程式部署至 Windows 上的 Azure App 服務，這會在 [IIS](#iis)上裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-229">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="7f6dd-230">Blazor WebAssembly目前不支援將獨立應用程式部署至適用于 Linux 的 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-230">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="7f6dd-231">目前無法使用 Linux 伺服器映射來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-231">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="7f6dd-232">正在進行工作，以啟用此案例。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-232">Work is in progress to enable this scenario.</span></span>

### <a name="azure-static-web-app"></a><span data-ttu-id="7f6dd-233">Azure 靜態 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="7f6dd-233">Azure static web app</span></span>

<span data-ttu-id="7f6dd-234">如需詳細資訊，請參閱 [教學課程：使用 Blazor Azure 靜態建立靜態 web 應用程式 Web Apps](/azure/static-web-apps/deploy-blazor)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-234">For more information, see [Tutorial: Building a static web app with Blazor in Azure Static Web Apps](/azure/static-web-apps/deploy-blazor).</span></span>

### <a name="iis"></a><span data-ttu-id="7f6dd-235">IIS</span><span class="sxs-lookup"><span data-stu-id="7f6dd-235">IIS</span></span>

<span data-ttu-id="7f6dd-236">IIS 是適用于應用程式的可用靜態檔案伺服器 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-236">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="7f6dd-237">若要設定 IIS 以裝載 Blazor ，請參閱 [在 Iis 上建立靜態網站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-237">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="7f6dd-238">已發佈的資產會在 `/bin/Release/{TARGET FRAMEWORK}/publish` 資料夾中建立。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-238">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="7f6dd-239">在 `publish` web 伺服器或主機服務上裝載資料夾的內容。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-239">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="7f6dd-240">web.config</span><span class="sxs-lookup"><span data-stu-id="7f6dd-240">web.config</span></span>

<span data-ttu-id="7f6dd-241">Blazor發行專案時， `web.config` 會使用下列 IIS 設定來建立檔案：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-241">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="7f6dd-242">針對下列副檔名設定 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-242">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="7f6dd-243">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="7f6dd-243">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="7f6dd-244">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="7f6dd-244">`.json`: `application/json`</span></span>
  * <span data-ttu-id="7f6dd-245">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="7f6dd-245">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="7f6dd-246">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7f6dd-246">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="7f6dd-247">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7f6dd-247">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="7f6dd-248">針對下列 MIME 類型會啟用 HTTP 壓縮：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-248">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="7f6dd-249">建立 URL Rewrite Module 規則：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-249">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="7f6dd-250">提供應用程式的靜態資產位於 () 的子目錄 `wwwroot/{PATH REQUESTED}` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-250">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="7f6dd-251">建立 SPA 回路由，讓非檔案資產的要求重新導向至其靜態資產資料夾中的應用程式預設檔 (`wwwroot/index.html`) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-251">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="7f6dd-252">使用自訂 web.config</span><span class="sxs-lookup"><span data-stu-id="7f6dd-252">Use a custom web.config</span></span>

<span data-ttu-id="7f6dd-253">若要使用自訂檔案 `web.config` ，請將自訂檔案放 `web.config` 在專案資料夾的根目錄。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-253">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="7f6dd-254">使用在應用程式的專案檔中，將專案設定為發佈 IIS 特定的資產 `PublishIISAssets` ，併發布專案：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-254">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="7f6dd-255">安裝 URL Rewrite 模組</span><span class="sxs-lookup"><span data-stu-id="7f6dd-255">Install the URL Rewrite Module</span></span>

<span data-ttu-id="7f6dd-256">需要 [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite)，才可重寫 URL。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-256">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="7f6dd-257">預設不會安裝此模組，且其無法用來安裝為網頁伺服器 (IIS) 角色服務功能。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-257">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="7f6dd-258">必須從 IIS 網站下載模組。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-258">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="7f6dd-259">請使用 Web Platform Installer 安裝模組：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-259">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="7f6dd-260">在本機上，巡覽至 [URL Rewrite Module 下載頁面](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-260">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="7f6dd-261">如需英文版，請選取 [WebPI] 下載 WebPI 安裝程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-261">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="7f6dd-262">如需其他語言，請選取適當的伺服器架構 (x86 x64) 來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-262">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="7f6dd-263">將安裝程式複製到伺服器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-263">Copy the installer to the server.</span></span> <span data-ttu-id="7f6dd-264">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-264">Run the installer.</span></span> <span data-ttu-id="7f6dd-265">選取 [安裝] 按鈕，並接受授權條款。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-265">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="7f6dd-266">安裝完成之後，不需要重新啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-266">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="7f6dd-267">設定網站</span><span class="sxs-lookup"><span data-stu-id="7f6dd-267">Configure the website</span></span>

<span data-ttu-id="7f6dd-268">將網站的 [實體路徑] 設為應用程式的資料夾。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-268">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="7f6dd-269">此資料夾包含：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-269">The folder contains:</span></span>

* <span data-ttu-id="7f6dd-270">`web.config`IIS 用來設定網站的檔案，包括必要的重新導向規則和檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-270">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="7f6dd-271">應用程式的靜態資產資料夾。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-271">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="7f6dd-272">作為 IIS 子應用程式的主機</span><span class="sxs-lookup"><span data-stu-id="7f6dd-272">Host as an IIS sub-app</span></span>

<span data-ttu-id="7f6dd-273">如果獨立應用程式裝載為 IIS 子應用程式，請執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-273">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="7f6dd-274">停用繼承的 ASP.NET Core 模組處理常式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-274">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="7f6dd-275">Blazor `web.config` 將區段新增至檔案，以移除應用程式已發佈檔案中的處理常式 `<handlers>` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-275">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="7f6dd-276">`<system.webServer>`使用 `<location>` `inheritInChildApplications` 設定為下列專案的元素，停用根 (父) 應用程式區段的繼承 `false` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-276">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="7f6dd-277">除了設定 [應用程式的基底路徑](xref:blazor/host-and-deploy/index#app-base-path)之外，還會執行移除處理常式或停用繼承。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-277">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="7f6dd-278">將應用程式檔案中的應用程式基底路徑設定 `index.html` 為在 iis 中設定子應用程式時所使用的 iis 別名。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-278">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="7f6dd-279">Brotli 和 Gzip 壓縮</span><span class="sxs-lookup"><span data-stu-id="7f6dd-279">Brotli and Gzip compression</span></span>

<span data-ttu-id="7f6dd-280">*本節僅適用于獨立的 Blazor WebAssembly 應用程式。裝載 Blazor 的應用程式會使用預設的 ASP.NET Core 應用程式 `web.config` 檔，而不是本節所連結的檔案。*</span><span class="sxs-lookup"><span data-stu-id="7f6dd-280">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="7f6dd-281">您可以透過設定 IIS `web.config` ，為獨立應用程式提供 Brotli 或 Gzip 壓縮 Blazor 的資產 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-281">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="7f6dd-282">如需範例設定檔，請參閱 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-282">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="7f6dd-283">在下列情況下，可能需要額外的範例檔案設定 `web.config` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-283">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="7f6dd-284">應用程式的規格會呼叫下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-284">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="7f6dd-285">提供範例檔案未設定的壓縮檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-285">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="7f6dd-286">以未壓縮的格式提供範例檔案所設定的壓縮檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-286">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="7f6dd-287">伺服器的 IIS 設定 (例如， `applicationHost.config`) 提供伺服器層級的 iis 預設值。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-287">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="7f6dd-288">根據伺服器層級的設定，應用程式可能需要與範例檔案所包含的 IIS 設定不同 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-288">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="7f6dd-289">疑難排解</span><span class="sxs-lookup"><span data-stu-id="7f6dd-289">Troubleshooting</span></span>

<span data-ttu-id="7f6dd-290">如果收到「500 - 內部伺服器錯誤」，且 IIS 管理員在嘗試存取網站設定時擲回錯誤，請確認是否已安裝 URL Rewrite 模組。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-290">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="7f6dd-291">未安裝模組時，IIS 無法剖析該檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-291">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="7f6dd-292">這可防止 IIS 管理員從服務的靜態檔案載入網站的設定和網站 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-292">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="7f6dd-293">如需針對部署至 IIS 進行疑難排解的詳細資訊，請參閱 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-293">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="7f6dd-294">Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="7f6dd-294">Azure Storage</span></span>

<span data-ttu-id="7f6dd-295">[Azure 儲存體](/azure/storage/) 的靜態檔案裝載允許無伺服器 Blazor 應用程式裝載。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-295">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="7f6dd-296">支援自訂網域名稱、Azure 內容傳遞網路 (CDN) 及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-296">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="7f6dd-297">當 Blob 服務針對儲存體帳戶上的靜態網站裝載啟用時：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-297">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="7f6dd-298">將 [索引文件名稱] 設定為 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-298">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="7f6dd-299">將 [錯誤文件路徑] 設定為 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-299">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="7f6dd-300">Razor 元件和其他非檔案端點不會位於 blob 服務所儲存之靜態內容中的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-300">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="7f6dd-301">當收到路由器應處理之其中一個資源的要求時 Blazor ，blob 服務所產生的 *404-找不* 到錯誤會將要求路由傳送至 **錯誤檔路徑**。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-301">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="7f6dd-302">`index.html`會傳回 blob，而且路由器會 Blazor 載入並處理路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-302">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="7f6dd-303">如果檔案的標頭中有不適當的 MIME 類型，則不會在執行時間載入檔案 `Content-Type` ，請採取下列其中一項動作：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-303">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="7f6dd-304">設定您的工具，在部署檔案時) 設定正確的 MIME 類型 (`Content-Type` 標頭。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-304">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="7f6dd-305">在部署應用程式之後，變更檔案的 MIME 類型 (`Content-Type` 標頭) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-305">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="7f6dd-306">在儲存體總管 (Azure 入口網站每個檔案的) ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-306">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="7f6dd-307">以滑鼠右鍵按一下檔案，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-307">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="7f6dd-308">設定 **ContentType** ，然後選取 [ **儲存** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-308">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="7f6dd-309">如需詳細資訊，請參閱 [Azure 儲存體中的靜態網站裝載](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-309">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="7f6dd-310">Nginx</span><span class="sxs-lookup"><span data-stu-id="7f6dd-310">Nginx</span></span>

<span data-ttu-id="7f6dd-311">下列檔案 `nginx.conf` 已簡化，示範如何設定 Nginx，以便在 `index.html` 每次找不到磁片上的對應檔案時傳送檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-311">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="7f6dd-312">使用設定 NGINX 高載 [速率限制](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) 時 [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) ， Blazor WebAssembly 應用程式可能需要大型的 `burst` 參數值，以容納應用程式所提出的相對大量要求。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-312">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="7f6dd-313">一開始，請將值設定為至少60：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-313">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="7f6dd-314">如果瀏覽器開發人員工具或網路流量工具表示要求收到 *503-服務無法使用* 的狀態碼，請增加值。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-314">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="7f6dd-315">如需生產環境 Nginx 網頁伺服器組態的詳細資訊，請參閱[建立 NGINX Plus 和 NGINX 組態檔](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-315">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="apache"></a><span data-ttu-id="7f6dd-316">Apache</span><span class="sxs-lookup"><span data-stu-id="7f6dd-316">Apache</span></span>

<span data-ttu-id="7f6dd-317">若要將 Blazor WebAssembly 應用程式部署至 CentOS 7 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-317">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="7f6dd-318">建立 Apache 設定檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-318">Create the Apache configuration file.</span></span> <span data-ttu-id="7f6dd-319">下列範例是簡化的設定檔 (`blazorapp.config`) ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-319">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="7f6dd-320">將 Apache 設定檔放入 `/etc/httpd/conf.d/` 目錄中，這是 CentOS 7 中的預設 Apache 設定目錄。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-320">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="7f6dd-321">將應用程式的檔案放入 `/var/www/blazorapp` 目錄中， (`DocumentRoot` 設定檔) 中指定的位置。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-321">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="7f6dd-322">重新開機 Apache 服務。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-322">Restart the Apache service.</span></span>

<span data-ttu-id="7f6dd-323">如需詳細資訊，請參閱 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-323">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="7f6dd-324">GitHub 頁面</span><span class="sxs-lookup"><span data-stu-id="7f6dd-324">GitHub Pages</span></span>

<span data-ttu-id="7f6dd-325">若要處理 URL 重寫，請新增具有腳本的檔案，以處理將要求重新導向 `wwwroot/404.html` 至頁面的腳本 `index.html` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-325">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="7f6dd-326">如需範例，請參閱 [SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-326">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="7f6dd-327">[即時網站](https://stevesandersonms.github.io/BlazorOnGitHubPages/)) </span><span class="sxs-lookup"><span data-stu-id="7f6dd-327">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="7f6dd-328">使用專案網站而非組織網站時，請 `<base>` 在中更新標記 `wwwroot/index.html` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-328">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="7f6dd-329">`href`使用尾端斜線將屬性值設定為 GitHub 存放庫名稱 (例如 `/my-repository/`) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-329">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="7f6dd-330">在[SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)中， `href` [ `.github/workflows/main.yml` 設定檔](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)會在發佈時更新基底。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-330">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="7f6dd-331">[SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)並非由 .Net Foundation 或 Microsoft 所擁有、維護或支援。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-331">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="7f6dd-332">主機組態值</span><span class="sxs-lookup"><span data-stu-id="7f6dd-332">Host configuration values</span></span>

<span data-ttu-id="7f6dd-333">在開發環境中， [ Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)可以在執行時間接受下列主機配置值作為命令列引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-333">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="7f6dd-334">內容根目錄</span><span class="sxs-lookup"><span data-stu-id="7f6dd-334">Content root</span></span>

<span data-ttu-id="7f6dd-335">`--contentroot`引數會將包含應用程式內容檔案的目錄絕對路徑設定 ([內容根目錄](xref:fundamentals/index#content-root)) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-335">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="7f6dd-336">在下列範例中，`/content-root-path` 是應用程式的內容根路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-336">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="7f6dd-337">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-337">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7f6dd-338">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-338">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="7f6dd-339">`launchSettings.json`在 **IIS Express** 設定檔中，將專案新增至應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-339">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7f6dd-340">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-340">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="7f6dd-341">在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數** 中指定引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-341">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7f6dd-342">在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-342">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="7f6dd-343">路徑基底</span><span class="sxs-lookup"><span data-stu-id="7f6dd-343">Path base</span></span>

<span data-ttu-id="7f6dd-344">`--pathbase`引數會將應用程式的應用程式基底路徑設定為使用非根相對 URL 路徑在本機執行 (將 `<base>` `href` 標籤設定為 `/` 暫存和生產) 以外的路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-344">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="7f6dd-345">在下列範例中，`/relative-URL-path` 是應用程式的路徑基底。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-345">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="7f6dd-346">如需詳細資訊，請參閱 [應用程式基底路徑](xref:blazor/host-and-deploy/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-346">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7f6dd-347">不同於提供給 `<base>` 標籤 `href` 的路徑，在傳遞 `--pathbase` 引數值時請勿包含尾端的斜線 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-347">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="7f6dd-348">如果應用程式基底路徑提供於 `<base>` 標籤作為 `<base href="/CoolApp/">` (包括尾端的斜線)，請將命令列引數值傳遞為 `--pathbase=/CoolApp` (不含尾端的斜線)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-348">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="7f6dd-349">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-349">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7f6dd-350">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-350">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="7f6dd-351">`launchSettings.json`在 **IIS Express** 設定檔中，將專案新增至應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-351">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7f6dd-352">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-352">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="7f6dd-353">在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數** 中指定引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-353">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7f6dd-354">在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-354">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="7f6dd-355">URL</span><span class="sxs-lookup"><span data-stu-id="7f6dd-355">URLs</span></span>

<span data-ttu-id="7f6dd-356">`--urls` 引數會搭配要用來接聽要求的連接埠和通訊協定，設定 IP 位址或主機位址。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-356">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="7f6dd-357">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-357">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7f6dd-358">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-358">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="7f6dd-359">`launchSettings.json`在 **IIS Express** 設定檔中，將專案新增至應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-359">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7f6dd-360">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-360">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="7f6dd-361">在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數** 中指定引數。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-361">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7f6dd-362">在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-362">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="7f6dd-363">設定修剪器</span><span class="sxs-lookup"><span data-stu-id="7f6dd-363">Configure the Trimmer</span></span>

<span data-ttu-id="7f6dd-364">Blazor 在每個發行組建上執行中繼語言 (IL) 修剪，以從輸出元件中移除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-364">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="7f6dd-365">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-trimmer>。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-365">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="7f6dd-366">設定連結器</span><span class="sxs-lookup"><span data-stu-id="7f6dd-366">Configure the Linker</span></span>

<span data-ttu-id="7f6dd-367">Blazor 在每個發行組建上執行中繼語言 (IL) 連結，以從輸出元件中移除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-367">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="7f6dd-368">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-368">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="7f6dd-369">自訂開機資源載入</span><span class="sxs-lookup"><span data-stu-id="7f6dd-369">Custom boot resource loading</span></span>

<span data-ttu-id="7f6dd-370">您 Blazor WebAssembly 可以使用函式來初始化應用程式 `loadBootResource` ，以覆寫內建的開機資源載入機制。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-370">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="7f6dd-371">用於 `loadBootResource` 下列案例：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-371">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="7f6dd-372">允許使用者載入靜態資源，例如時區資料或 `dotnet.wasm` CDN。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-372">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="7f6dd-373">使用 HTTP 要求載入壓縮的元件，並在不支援從伺服器提取壓縮內容的主機用戶端上解壓縮這些元件。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-373">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="7f6dd-374">將每個要求重新導向至新名稱，將別名資源重新導向至不同的名稱 `fetch` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-374">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="7f6dd-375">`loadBootResource` 參數會出現在下表中。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-375">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="7f6dd-376">參數</span><span class="sxs-lookup"><span data-stu-id="7f6dd-376">Parameter</span></span>    | <span data-ttu-id="7f6dd-377">描述</span><span class="sxs-lookup"><span data-stu-id="7f6dd-377">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="7f6dd-378">資源類型。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-378">The type of the resource.</span></span> <span data-ttu-id="7f6dd-379">運算子類型： `assembly` 、 `pdb` 、 `dotnetjs` 、 `dotnetwasm` 、 `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="7f6dd-379">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="7f6dd-380">資源名稱。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-380">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="7f6dd-381">資源的相對或絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-381">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="7f6dd-382">代表回應中預期內容的完整性字串。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-382">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="7f6dd-383">`loadBootResource` 傳回下列任一項以覆寫載入進程：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-383">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="7f6dd-384">URI 字串。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-384">URI string.</span></span> <span data-ttu-id="7f6dd-385">在下列範例 (`wwwroot/index.html`) 中，從 CDN 提供下列檔案 `https://my-awesome-cdn.com/` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-385">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="7f6dd-386">時區資料</span><span class="sxs-lookup"><span data-stu-id="7f6dd-386">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="7f6dd-387">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="7f6dd-387">`Promise<Response>`.</span></span> <span data-ttu-id="7f6dd-388">`integrity`在標頭中傳遞參數，以保留預設的完整性檢查行為。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-388">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="7f6dd-389">下列範例 (`wwwroot/index.html`) 將自訂 HTTP 標頭新增至輸出要求，並將 `integrity` 參數傳遞至 `fetch` 呼叫：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-389">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="7f6dd-390">`null`/`undefined`，這會導致預設的載入行為。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-390">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="7f6dd-391">外部來源必須傳回瀏覽器所需的 CORS 標頭，以允許跨原始來源資源載入。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-391">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="7f6dd-392">依預設，Cdn 通常會提供必要的標頭。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-392">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="7f6dd-393">您只需要指定自訂行為的類型。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-393">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="7f6dd-394">未指定的類型 `loadBootResource` 會根據其預設載入行為載入架構。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-394">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="7f6dd-395">變更 DLL 檔案的副檔名</span><span class="sxs-lookup"><span data-stu-id="7f6dd-395">Change the filename extension of DLL files</span></span>

<span data-ttu-id="7f6dd-396">如果您需要變更應用程式已發佈檔案的副檔名 `.dll` ，請遵循本節中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-396">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="7f6dd-397">發佈應用程式之後，請使用 shell 腳本或 DevOps 組建管線，將檔案重新命名 `.dll` 以使用不同的副檔名。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-397">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="7f6dd-398">以 `.dll` `wwwroot` 應用程式發佈輸出的目錄中的檔案為目標 (例如 `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-398">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="7f6dd-399">在下列範例中， `.dll` 會將檔案重新命名為使用 `.bin` 副檔名。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-399">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="7f6dd-400">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-400">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="7f6dd-401">如果服務工作者資產也在使用中，請新增下列命令：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-401">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="7f6dd-402">在 Linux 或 macOS 上：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-402">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="7f6dd-403">如果服務工作者資產也在使用中，請新增下列命令：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-403">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="7f6dd-404">若要使用不同的副檔名 `.bin` ，請 `.bin` 在上述命令中取代。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-404">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="7f6dd-405">若要處理壓縮 `blazor.boot.json.gz` 和檔案 `blazor.boot.json.br` ，請採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-405">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="7f6dd-406">移除壓縮的 `blazor.boot.json.gz` 和檔案 `blazor.boot.json.br` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-406">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="7f6dd-407">這種方法會停用壓縮。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-407">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="7f6dd-408">重新壓縮更新後的檔案 `blazor.boot.json` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-408">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="7f6dd-409">先前的指導方針也適用于服務工作者資產正在使用中。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-409">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="7f6dd-410">移除或重新壓縮 `wwwroot/service-worker-assets.js.br` 和 `wwwroot/service-worker-assets.js.gz` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-410">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="7f6dd-411">否則，瀏覽器中的檔案完整性檢查會失敗。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-411">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="7f6dd-412">下列 Windows 範例使用放置於專案根目錄的 PowerShell 腳本。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-412">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="7f6dd-413">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="7f6dd-413">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="7f6dd-414">如果服務工作者資產也在使用中，請新增下列命令：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-414">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="7f6dd-415">在專案檔中，腳本會在發佈應用程式之後執行：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-415">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="7f6dd-416">重新命名和消極式載入相同的元件時，請參閱中的指導方針 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-416">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="7f6dd-417">解決完整性檢查失敗</span><span class="sxs-lookup"><span data-stu-id="7f6dd-417">Resolve integrity check failures</span></span>

<span data-ttu-id="7f6dd-418">當 Blazor WebAssembly 下載應用程式的啟動檔案時，它會指示瀏覽器對回應執行完整性檢查。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-418">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="7f6dd-419">它會使用檔案中的資訊 `blazor.boot.json` `.dll` ，為、和其他檔案指定預期的 256 sha-1 雜湊值 `.wasm` 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-419">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="7f6dd-420">這項功能很有用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-420">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="7f6dd-421">它可確保您不會在載入不一致的檔案集時產生風險，例如，當使用者在下載應用程式檔的過程中，將新的部署套用至您的 web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-421">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="7f6dd-422">不一致的檔案可能會導致未定義的行為。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-422">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="7f6dd-423">它可確保使用者的瀏覽器永遠不會快取不一致或不正確回應，這可能會讓他們無法啟動應用程式，即使它們手動重新整理頁面也是如此。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-423">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="7f6dd-424">這可讓您安全地快取回應，甚至不檢查伺服器端的變更，直到預期的 256 SHA-1 雜湊本身變更為止，因此後續頁面載入牽涉到較少的要求，並更快完成。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-424">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="7f6dd-425">如果您的 web 伺服器傳回的回應不符合預期的 256 SHA-1 雜湊，您將會在瀏覽器的開發人員主控台中看到類似下面的錯誤：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-425">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

> <span data-ttu-id="7f6dd-426">在 https://myapp.example.com/\_framework/My Blazor 計算 SHA-256 完整性 ' IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY = ' 的資源 'App.dll ' 的 ' 完整性 ' 屬性中找不到有效的摘要。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-426">Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/\_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='.</span></span> <span data-ttu-id="7f6dd-427">資源已被封鎖。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-427">The resource has been blocked.</span></span>

<span data-ttu-id="7f6dd-428">在大部分的情況下，這 *不* 是完整性檢查本身的問題。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-428">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="7f6dd-429">相反地，這表示有其他問題，而完整性檢查則會警告您有關該其他問題。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-429">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="7f6dd-430">診斷完整性問題</span><span class="sxs-lookup"><span data-stu-id="7f6dd-430">Diagnosing integrity problems</span></span>

<span data-ttu-id="7f6dd-431">建立應用程式時，產生的 `blazor.boot.json` 資訊清單會描述您開機資源的 256 sha-1 雜湊 (例如，、 `.dll` 以及 `.wasm` 產生組建輸出時) 的其他檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-431">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="7f6dd-432">只要 SHA-256 雜湊 `blazor.boot.json` 符合傳遞給瀏覽器的檔案，完整性檢查就會通過。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-432">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="7f6dd-433">這種失敗的常見原因如下：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-433">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="7f6dd-434">Web 服務器的回應是錯誤 (例如， *找不到 404* 或 *500-Internal server 錯誤*) ，而不是瀏覽器要求的檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-434">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="7f6dd-435">瀏覽器會將此報告為完整性檢查失敗，而不是回應失敗。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-435">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="7f6dd-436">在檔案的組建和傳遞之間，檔案的內容已變更至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-436">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="7f6dd-437">這可能會發生：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-437">This might happen:</span></span>
   * <span data-ttu-id="7f6dd-438">如果您或組建工具手動修改組建輸出。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-438">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="7f6dd-439">如果部署程式的某些層面修改了檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-439">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="7f6dd-440">例如，如果您使用以 Git 為基礎的部署機制，請記住，如果您在 Windows 上認可檔案並在 Linux 上查看檔案，則 Git 會以透明的方式將 Windows 樣式的行尾結束符號轉換為 Unix 樣式的行尾結束符號。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-440">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="7f6dd-441">變更檔案行尾結束符號會變更 256 SHA-1 雜湊。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-441">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="7f6dd-442">若要避免這個問題，請考慮 [使用 `.gitattributes` 將組建構件 `binary` 視為](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-442">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="7f6dd-443">Web 服務器會在提供檔案內容的過程中修改檔案內容。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-443">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="7f6dd-444">例如，某些內容散發網路 (Cdn) 會自動嘗試 [縮短](xref:client-side/bundling-and-minification#minification) HTML，進而修改它。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-444">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="7f6dd-445">您可能需要停用這類功能。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-445">You may need to disable such features.</span></span>

<span data-ttu-id="7f6dd-446">若要診斷下列哪一個適用于您的案例：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-446">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="7f6dd-447">注意哪個檔案會藉由讀取錯誤訊息來觸發錯誤。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-447">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="7f6dd-448">開啟瀏覽器的開發人員工具，並查看 [ *網路* ] 索引標籤。如有必要，請重載頁面以查看要求和回應的清單。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-448">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="7f6dd-449">尋找在該清單中觸發錯誤的檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-449">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="7f6dd-450">檢查回應中的 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-450">Check the HTTP status code in the response.</span></span> <span data-ttu-id="7f6dd-451">如果伺服器傳回 *200-OK* (或另一個2xx 狀態碼) 以外的任何一個，則表示您有伺服器端的問題要進行診斷。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-451">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="7f6dd-452">例如，狀態碼403表示有授權問題，而狀態碼500表示伺服器以未指定的方式失敗。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-452">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="7f6dd-453">請參閱伺服器端記錄來診斷並修正應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-453">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="7f6dd-454">如果資源的狀態碼是 *200-確定* ，請查看瀏覽器開發人員工具中的回應內容，並檢查內容是否符合預期的資料。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-454">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matches up with the data expected.</span></span> <span data-ttu-id="7f6dd-455">例如，常見的問題是 jeffv 路由，讓要求即使是其他檔案也會傳回您的 `index.html` 資料。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-455">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="7f6dd-456">請確定要求的回應 `.wasm` 是 WebAssembly 二進位檔，而要求的回應 `.dll` 是 .net 元件二進位檔。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-456">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="7f6dd-457">如果沒有，您就有伺服器端路由問題需要進行診斷。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-457">If not, you have a server-side routing problem to diagnose.</span></span>
 1. <span data-ttu-id="7f6dd-458">搜尋以使用 [疑難排解完整性 PowerShell 腳本](#troubleshoot-integrity-powershell-script)來驗證應用程式的已發佈和已部署輸出。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-458">Seek to validate the app's published and deployed output with the [Troubleshoot integrity PowerShell script](#troubleshoot-integrity-powershell-script).</span></span>

<span data-ttu-id="7f6dd-459">如果您確認伺服器傳回義正辭嚴正確的資料，就必須在檔案的組建和傳遞之間修改內容。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-459">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="7f6dd-460">若要調查這一點：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-460">To investigate this:</span></span>

 * <span data-ttu-id="7f6dd-461">檢查組建工具鏈和部署機制，以防在建立檔案之後修改檔案。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-461">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="7f6dd-462">例如，如先前所述，Git 會轉換檔案行尾結束符號。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-462">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="7f6dd-463">檢查 web 伺服器或 CDN 設定，以防它們設定為動態修改回應 (例如，嘗試縮短 HTML) 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-463">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="7f6dd-464">Web 服務器可正常執行 HTTP 壓縮 (例如，傳回 `content-encoding: br` 或 `content-encoding: gzip`) ，因為這不會影響解壓縮之後的結果。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-464">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="7f6dd-465">不過，網頁伺服器 *不* 能修改未壓縮的資料。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-465">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="troubleshoot-integrity-powershell-script"></a><span data-ttu-id="7f6dd-466">針對完整性 PowerShell 腳本進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="7f6dd-466">Troubleshoot integrity PowerShell script</span></span>

<span data-ttu-id="7f6dd-467">使用 [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell 腳本來驗證已發佈和已部署的 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-467">Use the [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell script to validate a published and deployed Blazor app.</span></span> <span data-ttu-id="7f6dd-468">當應用程式有架構無法識別的完整性問題時，就會提供此腳本作為起點 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-468">The script is provided as a starting point when the app has integrity issues that the Blazor framework can't identify.</span></span> <span data-ttu-id="7f6dd-469">您的應用程式可能需要自訂腳本。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-469">Customization of the script might be required for your apps.</span></span>

<span data-ttu-id="7f6dd-470">腳本會檢查資料夾中的檔案 `publish` ，並從已部署的應用程式下載，以偵測包含完整性雜湊之不同資訊清單中的問題。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-470">The script checks the files in the `publish` folder and downloaded from the deployed app to detect issues in the different manifests that contain integrity hashes.</span></span> <span data-ttu-id="7f6dd-471">這些檢查應該會偵測到最常見的問題：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-471">These checks should detect the most common problems:</span></span>

* <span data-ttu-id="7f6dd-472">您已在已發行的輸出中修改檔案，但未加以察覺。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-472">You modified a file in the published output without realizing it.</span></span>
* <span data-ttu-id="7f6dd-473">應用程式未正確部署至部署目標，或部署目標環境內的某個變更。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-473">The app wasn't correctly deployed to the deployment target, or something changed within the deployment target's environment.</span></span>
* <span data-ttu-id="7f6dd-474">已部署的應用程式和發行應用程式的輸出之間有一些差異。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-474">There are differences between the deployed app and the output from publishing the app.</span></span>

<span data-ttu-id="7f6dd-475">在 PowerShell 命令 shell 中使用下列命令叫用腳本：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-475">Invoke the script with the following command in a PowerShell command shell:</span></span>

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

<span data-ttu-id="7f6dd-476">占 位 符：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-476">Placeholders:</span></span>

* <span data-ttu-id="7f6dd-477">`{BASE URL}`：已部署應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-477">`{BASE URL}`: The URL of the deployed app.</span></span>
* <span data-ttu-id="7f6dd-478">`{PUBLISH OUTPUT FOLDER}`：應用程式用來 `publish` 部署應用程式的資料夾或位置的路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-478">`{PUBLISH OUTPUT FOLDER}`: The path to the app's `publish` folder or location where the app is published for deployment.</span></span>

> [!NOTE]
> <span data-ttu-id="7f6dd-479">若要將 `dotnet/AspNetCore.Docs` GitHub 存放庫複製到使用 [Bitdefender](https://www.bitdefender.com) 病毒掃描程式的系統，請將例外狀況新增至 `integrity.ps1` 腳本的 Bitdefender。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-479">To clone the `dotnet/AspNetCore.Docs` GitHub repository to a system that uses the [Bitdefender](https://www.bitdefender.com) virus scanner, add an exception to Bitdefender for the `integrity.ps1` script.</span></span> <span data-ttu-id="7f6dd-480">在複製存放庫之前，將例外狀況新增至 Bitdefender，以避免病毒掃描程式隔離腳本。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-480">Add the exception to Bitdefender before cloning the repo to avoid having the script quarantined by the virus scanner.</span></span> <span data-ttu-id="7f6dd-481">下列範例是 Windows 系統上複製之存放庫的腳本一般路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-481">The following example is a typical path to the script for the cloned repo on a Windows system.</span></span> <span data-ttu-id="7f6dd-482">視需要調整路徑。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-482">Adjust the path as needed.</span></span> <span data-ttu-id="7f6dd-483">預留位置 `{USER}` 是使用者的路徑區段。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-483">The placeholder `{USER}` is the user's path segment.</span></span>
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="7f6dd-484">停用非 PWA 應用程式的完整性檢查</span><span class="sxs-lookup"><span data-stu-id="7f6dd-484">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="7f6dd-485">在大多數情況下，請勿停用完整性檢查。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-485">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="7f6dd-486">停用完整性檢查並無法解決造成非預期回應的根本問題，因而導致遺失先前所列的權益。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-486">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="7f6dd-487">在某些情況下，無法依賴網頁伺服器傳回一致的回應，而且您沒有任何選擇，而是停用完整性檢查。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-487">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="7f6dd-488">若要停用完整性檢查，請將下列內容新增至專案檔中的屬性群組 Blazor WebAssembly `.csproj` ：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-488">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="7f6dd-489">`BlazorCacheBootResources` 也會 Blazor `.dll` 根據其 256 sha-1 雜湊停用快取、和其他檔案的預設行為， `.wasm` 因為屬性指出無法依賴 sha-256 雜湊來取得正確性。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-489">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="7f6dd-490">即使使用此設定，瀏覽器的一般 HTTP 快取仍可能會快取這些檔案，但這是否發生，取決於您的 web 伺服器設定和它所提供的 `cache-control` 標頭。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-490">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="7f6dd-491">`BlazorCacheBootResources`屬性不會停用[漸進式 Web 應用程式的完整性檢查 (pwa) ](xref:blazor/progressive-web-app)。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-491">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="7f6dd-492">如需 Pwa 的相關指引，請參閱 [停用 pwa 的完整性檢查](#disable-integrity-checking-for-pwas) 一節。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-492">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="7f6dd-493">停用 Pwa 的完整性檢查</span><span class="sxs-lookup"><span data-stu-id="7f6dd-493">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="7f6dd-494">Blazor的漸進式 Web 應用程式 (PWA) 範本包含建議的檔案 `service-worker.published.js` ，該檔案負責提取和儲存應用程式檔，以供離線使用。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-494">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="7f6dd-495">這是與一般應用程式啟動機制不同的程式，且具有自己的個別完整性檢查邏輯。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-495">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="7f6dd-496">在檔案中 `service-worker.published.js` ，下列程式程式碼存在：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-496">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="7f6dd-497">若要停用完整性檢查，請將這 `integrity` 一行變更為下列程式碼來移除參數：</span><span class="sxs-lookup"><span data-stu-id="7f6dd-497">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="7f6dd-498">同樣地，停用完整性檢查即表示您遺失完整性檢查所提供的安全性保證。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-498">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="7f6dd-499">例如，如果使用者的瀏覽器在您部署新版本的確切時刻快取了應用程式，則可能會有一個風險，它可以從舊的部署快取一些檔案，而有些則會從新的部署中快取。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-499">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="7f6dd-500">如果發生這種情況，應用程式就會變成處於中斷狀態，直到您部署進一步的更新。</span><span class="sxs-lookup"><span data-stu-id="7f6dd-500">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
