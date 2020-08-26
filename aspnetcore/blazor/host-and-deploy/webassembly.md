---
title: 裝載和部署 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何 Blazor 使用 ASP.NET Core、內容傳遞網路 (CDN) 、檔案伺服器和 GitHub 頁面來裝載和部署應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 6b4c3d55d77af104c969cac0fcbf642f35c7dd7f
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865260"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="20077-103">裝載和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="20077-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="20077-104">[Luke Latham](https://github.com/guardrex)、 [Rainer Stropek](https://www.timecockpit.com)、 [Daniel Roth](https://github.com/danroth27)、 [Ben Adams](https://twitter.com/ben_a_adams)及[Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="20077-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="20077-105">使用[ Blazor WebAssembly 裝載模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="20077-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="20077-106">Blazor應用程式、其相依性和 .net 執行時間會以平行方式下載至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="20077-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="20077-107">應用程式會直接在瀏覽器 UI 執行緒上執行。</span><span class="sxs-lookup"><span data-stu-id="20077-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="20077-108">以下是支援的部署策略：</span><span class="sxs-lookup"><span data-stu-id="20077-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="20077-109">Blazor應用程式是由 ASP.NET Core 應用程式提供服務。</span><span class="sxs-lookup"><span data-stu-id="20077-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="20077-110">此策略已於[搭配 ASP.NET Core 的已裝載部署](#hosted-deployment-with-aspnet-core)一節中涵蓋。</span><span class="sxs-lookup"><span data-stu-id="20077-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="20077-111">Blazor應用程式會放置在靜態裝載 web 伺服器或服務上，而不會使用 .net 來提供 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="20077-112">此策略包含在 [獨立部署](#standalone-deployment) 區段中，其中包含將 Blazor WebAssembly 應用程式裝載為 IIS 子應用程式的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="20077-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="20077-113">壓縮</span><span class="sxs-lookup"><span data-stu-id="20077-113">Compression</span></span>

<span data-ttu-id="20077-114">Blazor WebAssembly發佈應用程式時，會在發行期間以靜態方式壓縮輸出，以減少應用程式的大小，並移除執行時間壓縮的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="20077-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="20077-115">使用的壓縮演算法如下：</span><span class="sxs-lookup"><span data-stu-id="20077-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="20077-116">[Brotli](https://tools.ietf.org/html/rfc7932) (最高層級) </span><span class="sxs-lookup"><span data-stu-id="20077-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="20077-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="20077-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="20077-118">Blazor 依賴主機提供適當的壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="20077-119">使用 ASP.NET Core 裝載的專案時，主專案可以執行內容協商以及提供靜態壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="20077-120">裝載 Blazor WebAssembly 獨立應用程式時，可能需要額外的工作，以確保提供靜態壓縮檔案：</span><span class="sxs-lookup"><span data-stu-id="20077-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="20077-121">如需 IIS `web.config` 壓縮設定，請參閱 [Iis： Brotli 和 Gzip 壓縮](#brotli-and-gzip-compression) 一節。</span><span class="sxs-lookup"><span data-stu-id="20077-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="20077-122">裝載在不支援靜態壓縮的檔案內容協商的靜態裝載方案（例如 GitHub 頁面）時，請考慮將應用程式設定為提取和解碼 Brotli 壓縮檔案：</span><span class="sxs-lookup"><span data-stu-id="20077-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="20077-123">從 [google/Brotli GitHub 存放庫](https://github.com/google/brotli)取得 JavaScript Brotli 解碼器。</span><span class="sxs-lookup"><span data-stu-id="20077-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="20077-124">從2020年7月起， `decode.min.js` 系統會在存放庫的[ `js` 資料夾](https://github.com/google/brotli/tree/master/js)中命名並重命名的解碼器檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="20077-125">更新應用程式以使用此解碼器。</span><span class="sxs-lookup"><span data-stu-id="20077-125">Update the app to use the decoder.</span></span> <span data-ttu-id="20077-126">將結束記號內的標記變更 `<body>` `wwwroot/index.html` 為下列內容：</span><span class="sxs-lookup"><span data-stu-id="20077-126">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="20077-127">若要停用壓縮，請在 `BlazorEnableCompression` 應用程式的專案檔中新增 MSBuild 屬性，並將值設定為 `false` ：</span><span class="sxs-lookup"><span data-stu-id="20077-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="20077-128">您 `BlazorEnableCompression` 可以 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 在命令 shell 中使用下列語法，將屬性傳遞至命令：</span><span class="sxs-lookup"><span data-stu-id="20077-128">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="20077-129">重寫 URL 以便正確地路由</span><span class="sxs-lookup"><span data-stu-id="20077-129">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="20077-130">應用程式中頁面元件的路由要求 Blazor WebAssembly ，與裝載應用程式中的路由要求一樣簡單 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="20077-130">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="20077-131">請考慮 Blazor WebAssembly 具有兩個元件的應用程式：</span><span class="sxs-lookup"><span data-stu-id="20077-131">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="20077-132">`Main.razor`：在應用程式的根目錄載入，並且包含 `About` 元件 () 的連結 `href="About"` 。</span><span class="sxs-lookup"><span data-stu-id="20077-132">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="20077-133">`About.razor`： `About` component。</span><span class="sxs-lookup"><span data-stu-id="20077-133">`About.razor`: `About` component.</span></span>

<span data-ttu-id="20077-134">使用瀏覽器的網址列要求應用程式預設文件時 (例如 `https://www.contoso.com/`)：</span><span class="sxs-lookup"><span data-stu-id="20077-134">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="20077-135">瀏覽器提出要求。</span><span class="sxs-lookup"><span data-stu-id="20077-135">The browser makes a request.</span></span>
1. <span data-ttu-id="20077-136">預設頁面會傳回，通常是 `index.html` 。</span><span class="sxs-lookup"><span data-stu-id="20077-136">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="20077-137">`index.html` 啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-137">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="20077-138">Blazor的路由器會載入，而 Razor `Main` 元件則會呈現。</span><span class="sxs-lookup"><span data-stu-id="20077-138">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="20077-139">在主頁面中，選取元件的連結可 `About` 在用戶端上運作，因為 Blazor 路由器會停止瀏覽器在網際網路上提出要求， `www.contoso.com` 並提供轉譯 `About` 的 `About` 元件本身。</span><span class="sxs-lookup"><span data-stu-id="20077-139">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="20077-140">\* Blazor WebAssembly 應用程式內\*內部端點的所有要求運作方式相同：要求不會對網際網路上伺服器裝載的資源觸發以瀏覽器為基礎的要求。</span><span class="sxs-lookup"><span data-stu-id="20077-140">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="20077-141">路由器會在內部處理要求。</span><span class="sxs-lookup"><span data-stu-id="20077-141">The router handles the requests internally.</span></span>

<span data-ttu-id="20077-142">如果使用瀏覽器之網址列提出對 `www.contoso.com/About` 的要求，則要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="20077-142">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="20077-143">在應用程式的網際網路主機上沒有這類資源存在，因此會傳回「404 - 找不到」\*\* 的回應。</span><span class="sxs-lookup"><span data-stu-id="20077-143">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="20077-144">因為瀏覽器會對以網際網路為基礎的主機發出要求，以提供用戶端頁面，所以 web 伺服器和主機服務必須將所有不在伺服器上的資源要求重寫為 `index.html` 頁面。</span><span class="sxs-lookup"><span data-stu-id="20077-144">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="20077-145">當 `index.html` 傳回時，應用程式的 Blazor 路由器會接管並回應正確的資源。</span><span class="sxs-lookup"><span data-stu-id="20077-145">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="20077-146">部署至 IIS 伺服器時，您可以使用 URL 重寫模組與應用程式的已發佈檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="20077-146">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="20077-147">如需詳細資訊，請參閱 [IIS](#iis) 一節。</span><span class="sxs-lookup"><span data-stu-id="20077-147">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="20077-148">搭配 ASP.NET Core 的已裝載部署</span><span class="sxs-lookup"><span data-stu-id="20077-148">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="20077-149">*託管部署* Blazor WebAssembly 可從 web 伺服器上執行的[ASP.NET Core 應用程式](xref:index)，將應用程式提供給瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="20077-149">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="20077-150">用戶端 Blazor WebAssembly 應用程式會 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 與伺服器應用程式的任何其他靜態 web 資產一起發行至伺服器應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="20077-150">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="20077-151">這兩個應用程式會一起部署。</span><span class="sxs-lookup"><span data-stu-id="20077-151">The two apps are deployed together.</span></span> <span data-ttu-id="20077-152">需要有能夠裝載 ASP.NET Core 應用程式的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="20077-152">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="20077-153">針對裝載的部署，當使用命令) 時，Visual Studio 包含\*\* Blazor WebAssembly 應用程式\*\*專案範本 (`blazorwasm` 範本， [`dotnet new`](/dotnet/core/tools/dotnet-new) **`Hosted`** (`-ho|--hosted` 使用命令) 時所選取的選項 `dotnet new` 。</span><span class="sxs-lookup"><span data-stu-id="20077-153">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="20077-154">如需 ASP.NET Core 應用程式裝載和部署的詳細資訊，請參閱 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="20077-154">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="20077-155">如需部署至 Azure App Service 的相關資訊，請參閱 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="20077-155">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="20077-156">具有多個應用程式的託管部署 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="20077-156">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="20077-157">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="20077-157">App configuration</span></span>

<span data-ttu-id="20077-158">若要設定託管 Blazor 解決方案來提供多個 Blazor WebAssembly 應用程式服務：</span><span class="sxs-lookup"><span data-stu-id="20077-158">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="20077-159">使用現有的主控 Blazor 方案，或從裝載的專案範本建立新的方案 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="20077-159">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="20077-160">在用戶端應用程式的專案檔中，將屬性新增至，並以的 `<StaticWebAssetBasePath>` `<PropertyGroup>` 值 `FirstApp` 設定專案靜態資產的基底路徑：</span><span class="sxs-lookup"><span data-stu-id="20077-160">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="20077-161">將第二個用戶端應用程式新增至解決方案：</span><span class="sxs-lookup"><span data-stu-id="20077-161">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="20077-162">將名為的資料夾加入 `SecondClient` 方案的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="20077-162">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="20077-163">在 Blazor WebAssembly `SecondBlazorApp.Client` 專案範本的資料夾中，建立名為的應用程式 `SecondClient` Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="20077-163">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="20077-164">在應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="20077-164">In the app's project file:</span></span>

    * <span data-ttu-id="20077-165">將屬性新增至，其 `<StaticWebAssetBasePath>` 值為 `<PropertyGroup>` `SecondApp` ：</span><span class="sxs-lookup"><span data-stu-id="20077-165">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="20077-166">將專案參考新增至 `Shared` 專案：</span><span class="sxs-lookup"><span data-stu-id="20077-166">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="20077-167">預留位置 `{SOLUTION NAME}` 是解決方案的名稱。</span><span class="sxs-lookup"><span data-stu-id="20077-167">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="20077-168">在伺服器應用程式的專案檔中，為新增的用戶端應用程式建立專案參考：</span><span class="sxs-lookup"><span data-stu-id="20077-168">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="20077-169">在伺服器應用程式的 `Properties/launchSettings.json` 檔案中，設定 `applicationUrl` Kestrel 設定檔 () 的， `{SOLUTION NAME}.Server` 以存取埠5001和5002上的用戶端應用程式：</span><span class="sxs-lookup"><span data-stu-id="20077-169">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="20077-170">在伺服器應用程式的 `Startup.Configure` 方法 (`Startup.cs`) 中，移除下列幾行，這些行會在呼叫之後出現 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ：</span><span class="sxs-lookup"><span data-stu-id="20077-170">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="20077-171">新增將要求對應至用戶端應用程式的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="20077-171">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="20077-172">下列範例會將中介軟體設定為在下列情況下執行：</span><span class="sxs-lookup"><span data-stu-id="20077-172">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="20077-173">針對已新增的用戶端應用程式，要求埠可能是5001（適用于原始用戶端應用程式）或5002。</span><span class="sxs-lookup"><span data-stu-id="20077-173">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="20077-174">要求主機 `firstapp.com` 適用于原始用戶端應用程式或 `secondapp.com` 新增的用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-174">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="20077-175">本節所示的範例需要下列各項的額外設定：</span><span class="sxs-lookup"><span data-stu-id="20077-175">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="20077-176">存取範例主機網域、和上的應用 `firstapp.com` 程式 `secondapp.com` 。</span><span class="sxs-lookup"><span data-stu-id="20077-176">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="20077-177">用戶端應用程式用來啟用 TLS 安全性的憑證 (HTTPS) 。</span><span class="sxs-lookup"><span data-stu-id="20077-177">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="20077-178">必要的設定已超出本文的範圍，並取決於裝載解決方案的方式。</span><span class="sxs-lookup"><span data-stu-id="20077-178">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="20077-179">如需詳細資訊，請參閱 [主機和部署文章](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="20077-179">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="20077-180">將下列程式碼放在先前移除的行：</span><span class="sxs-lookup"><span data-stu-id="20077-180">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="20077-181">在伺服器應用程式的氣象預報控制器 (`Controllers/WeatherForecastController.cs`) 中，將現有的路由 (`[Route("[controller]")]`) 取代為 `WeatherForecastController` 下列路由：</span><span class="sxs-lookup"><span data-stu-id="20077-181">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="20077-182">先前新增至伺服器應用程式方法的中介軟體會 `Startup.Configure` `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` 根據埠 (5001/5002) 或網域 (`firstapp.com` / `secondapp.com`) ，將連入要求修改至或。</span><span class="sxs-lookup"><span data-stu-id="20077-182">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="20077-183">需要上述的控制器路由，才能將天氣資料從伺服器應用程式傳回給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-183">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="20077-184">靜態資產和類別庫</span><span class="sxs-lookup"><span data-stu-id="20077-184">Static assets and class libraries</span></span>

<span data-ttu-id="20077-185">針對靜態資產，請使用下列方法：</span><span class="sxs-lookup"><span data-stu-id="20077-185">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="20077-186">當資產位於用戶端應用程式的 `wwwroot` 資料夾時，請正常提供其路徑：</span><span class="sxs-lookup"><span data-stu-id="20077-186">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="20077-187">當資產位於 `wwwroot` 類別庫的資料夾中時[ Razor (RCL) ](xref:blazor/components/class-libraries)，請依[RCL 文章](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)中的指導方針參考用戶端應用程式中的靜態資產：</span><span class="sxs-lookup"><span data-stu-id="20077-187">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="20077-188">類別庫提供給用戶端應用程式的元件會正常地參考。</span><span class="sxs-lookup"><span data-stu-id="20077-188">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="20077-189">如果有任何元件需要樣式表單或 JavaScript 檔案，請使用下列其中一種方法來取得靜態資產：</span><span class="sxs-lookup"><span data-stu-id="20077-189">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="20077-190">用戶端應用程式的檔案 `wwwroot/index.html` 可以將 (`<link>`) 連結到靜態資產。</span><span class="sxs-lookup"><span data-stu-id="20077-190">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="20077-191">元件可以使用架構的[ `Link` 元件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)來取得靜態資產。</span><span class="sxs-lookup"><span data-stu-id="20077-191">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="20077-192">上述的方法會在下列範例中示範。</span><span class="sxs-lookup"><span data-stu-id="20077-192">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="20077-193">類別庫提供給用戶端應用程式的元件會正常地參考。</span><span class="sxs-lookup"><span data-stu-id="20077-193">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="20077-194">如果有任何元件需要樣式表單或 JavaScript 檔案，用戶端應用程式的檔案 `wwwroot/index.html` 必須包含正確的靜態資產連結。</span><span class="sxs-lookup"><span data-stu-id="20077-194">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="20077-195">下列範例會示範這些方法。</span><span class="sxs-lookup"><span data-stu-id="20077-195">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="20077-196">將下列 `Jeep` 元件新增至其中一個用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-196">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="20077-197">`Jeep`元件使用：</span><span class="sxs-lookup"><span data-stu-id="20077-197">The `Jeep` component uses:</span></span>

* <span data-ttu-id="20077-198">從用戶端應用程式的 `wwwroot` 資料夾 () 的映射 `jeep-cj.png` 。</span><span class="sxs-lookup"><span data-stu-id="20077-198">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="20077-199">新增的元件連結 [ Razor 庫](xref:blazor/components/class-libraries) 中的映射 (`JeepImage`) `wwwroot` 資料夾 (`jeep-yj.png`) 。</span><span class="sxs-lookup"><span data-stu-id="20077-199">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="20077-200">`Component1`當連結 `JeepImage` 庫加入至方案時，RCL 專案範本會自動建立範例元件 () 。</span><span class="sxs-lookup"><span data-stu-id="20077-200">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="20077-201">除非您擁有映射， **否則請勿公開發布車輛** 的影像。</span><span class="sxs-lookup"><span data-stu-id="20077-201">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="20077-202">否則，您會面臨著作權侵權的風險。</span><span class="sxs-lookup"><span data-stu-id="20077-202">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="20077-203">程式庫的 `jeep-yj.png` 影像也可以新增至程式庫的 `Component1` 元件 (`Component1.razor`) 。</span><span class="sxs-lookup"><span data-stu-id="20077-203">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="20077-204">若要將 `my-component` CSS 類別提供給用戶端應用程式的頁面，請使用架構的[ `Link` 元件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)連結至程式庫的樣式表單：</span><span class="sxs-lookup"><span data-stu-id="20077-204">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="20077-205">使用[ `Link` 元件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)的另一種方式是從用戶端應用程式的檔案載入樣式表單 `wwwroot/index.html` 。</span><span class="sxs-lookup"><span data-stu-id="20077-205">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="20077-206">這種方法可讓用戶端應用程式中的所有元件使用樣式表單：</span><span class="sxs-lookup"><span data-stu-id="20077-206">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="20077-207">程式庫的 `jeep-yj.png` 映射也可以新增至程式庫的 `Component1` 元件 (`Component1.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="20077-207">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="20077-208">用戶端應用程式的檔案會 `wwwroot/index.html` 以下列新增的標記要求程式庫的樣式表單 `<link>` ：</span><span class="sxs-lookup"><span data-stu-id="20077-208">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="20077-209">將流覽新增至 `Jeep` 用戶端應用程式元件中的元件 `NavMenu` (`Shared/NavMenu.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="20077-209">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="20077-210">如需 RCLs 的詳細資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="20077-210">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="20077-211">獨立部署</span><span class="sxs-lookup"><span data-stu-id="20077-211">Standalone deployment</span></span>

<span data-ttu-id="20077-212">*獨立部署* Blazor WebAssembly 會以一組直接由用戶端要求的靜態檔案來提供應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-212">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="20077-213">任何靜態檔案伺服器都能提供 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-213">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="20077-214">獨立部署資產會發佈到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="20077-214">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="20077-215">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="20077-215">Azure App Service</span></span>

<span data-ttu-id="20077-216">Blazor WebAssembly 您可以將應用程式部署至 Windows 上的 Azure App 服務，這會在 [IIS](#iis)上裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-216">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="20077-217">Blazor WebAssembly目前不支援將獨立應用程式部署至適用于 Linux 的 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="20077-217">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="20077-218">目前無法使用 Linux 伺服器映射來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="20077-218">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="20077-219">正在進行工作，以啟用此案例。</span><span class="sxs-lookup"><span data-stu-id="20077-219">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="20077-220">IIS</span><span class="sxs-lookup"><span data-stu-id="20077-220">IIS</span></span>

<span data-ttu-id="20077-221">IIS 是適用于應用程式的可用靜態檔案伺服器 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="20077-221">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="20077-222">若要設定 IIS 以裝載 Blazor ，請參閱 [在 Iis 上建立靜態網站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="20077-222">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="20077-223">已發佈的資產會在 `/bin/Release/{TARGET FRAMEWORK}/publish` 資料夾中建立。</span><span class="sxs-lookup"><span data-stu-id="20077-223">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="20077-224">在 `publish` web 伺服器或主機服務上裝載資料夾的內容。</span><span class="sxs-lookup"><span data-stu-id="20077-224">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="20077-225">web.config</span><span class="sxs-lookup"><span data-stu-id="20077-225">web.config</span></span>

<span data-ttu-id="20077-226">Blazor發行專案時， `web.config` 會使用下列 IIS 設定來建立檔案：</span><span class="sxs-lookup"><span data-stu-id="20077-226">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="20077-227">針對下列副檔名設定 MIME 類型：</span><span class="sxs-lookup"><span data-stu-id="20077-227">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="20077-228">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="20077-228">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="20077-229">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="20077-229">`.json`: `application/json`</span></span>
  * <span data-ttu-id="20077-230">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="20077-230">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="20077-231">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="20077-231">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="20077-232">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="20077-232">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="20077-233">針對下列 MIME 類型會啟用 HTTP 壓縮：</span><span class="sxs-lookup"><span data-stu-id="20077-233">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="20077-234">建立 URL Rewrite Module 規則：</span><span class="sxs-lookup"><span data-stu-id="20077-234">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="20077-235">提供應用程式的靜態資產位於 () 的子目錄 `wwwroot/{PATH REQUESTED}` 。</span><span class="sxs-lookup"><span data-stu-id="20077-235">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="20077-236">建立 SPA 回路由，讓非檔案資產的要求重新導向至其靜態資產資料夾中的應用程式預設檔 (`wwwroot/index.html`) 。</span><span class="sxs-lookup"><span data-stu-id="20077-236">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="20077-237">使用自訂 web.config</span><span class="sxs-lookup"><span data-stu-id="20077-237">Use a custom web.config</span></span>

<span data-ttu-id="20077-238">若要使用自訂檔案 `web.config` ，請將自訂檔案放 `web.config` 在專案資料夾的根目錄，然後發佈專案。</span><span class="sxs-lookup"><span data-stu-id="20077-238">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="20077-239">安裝 URL Rewrite 模組</span><span class="sxs-lookup"><span data-stu-id="20077-239">Install the URL Rewrite Module</span></span>

<span data-ttu-id="20077-240">需要 [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite)，才可重寫 URL。</span><span class="sxs-lookup"><span data-stu-id="20077-240">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="20077-241">預設不會安裝此模組，且其無法用來安裝為網頁伺服器 (IIS) 角色服務功能。</span><span class="sxs-lookup"><span data-stu-id="20077-241">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="20077-242">必須從 IIS 網站下載模組。</span><span class="sxs-lookup"><span data-stu-id="20077-242">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="20077-243">請使用 Web Platform Installer 安裝模組：</span><span class="sxs-lookup"><span data-stu-id="20077-243">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="20077-244">在本機上，巡覽至 [URL Rewrite Module 下載頁面](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="20077-244">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="20077-245">如需英文版，請選取 [WebPI]\*\*\*\* 下載 WebPI 安裝程式。</span><span class="sxs-lookup"><span data-stu-id="20077-245">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="20077-246">如需其他語言，請選取適當的伺服器架構 (x86 x64) 來下載安裝程式。</span><span class="sxs-lookup"><span data-stu-id="20077-246">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="20077-247">將安裝程式複製到伺服器。</span><span class="sxs-lookup"><span data-stu-id="20077-247">Copy the installer to the server.</span></span> <span data-ttu-id="20077-248">執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="20077-248">Run the installer.</span></span> <span data-ttu-id="20077-249">選取 [安裝]\*\*\*\* 按鈕，並接受授權條款。</span><span class="sxs-lookup"><span data-stu-id="20077-249">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="20077-250">安裝完成之後，不需要重新啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="20077-250">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="20077-251">設定網站</span><span class="sxs-lookup"><span data-stu-id="20077-251">Configure the website</span></span>

<span data-ttu-id="20077-252">將網站的 [實體路徑]\*\*\*\* 設為應用程式的資料夾。</span><span class="sxs-lookup"><span data-stu-id="20077-252">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="20077-253">此資料夾包含：</span><span class="sxs-lookup"><span data-stu-id="20077-253">The folder contains:</span></span>

* <span data-ttu-id="20077-254">`web.config`IIS 用來設定網站的檔案，包括必要的重新導向規則和檔案內容類型。</span><span class="sxs-lookup"><span data-stu-id="20077-254">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="20077-255">應用程式的靜態資產資料夾。</span><span class="sxs-lookup"><span data-stu-id="20077-255">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="20077-256">作為 IIS 子應用程式的主機</span><span class="sxs-lookup"><span data-stu-id="20077-256">Host as an IIS sub-app</span></span>

<span data-ttu-id="20077-257">如果獨立應用程式裝載為 IIS 子應用程式，請執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="20077-257">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="20077-258">停用繼承的 ASP.NET Core 模組處理常式。</span><span class="sxs-lookup"><span data-stu-id="20077-258">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="20077-259">Blazor `web.config` 將區段新增至檔案，以移除應用程式已發佈檔案中的處理常式 `<handlers>` ：</span><span class="sxs-lookup"><span data-stu-id="20077-259">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="20077-260">`<system.webServer>`使用 `<location>` `inheritInChildApplications` 設定為下列專案的元素，停用根 (父) 應用程式區段的繼承 `false` ：</span><span class="sxs-lookup"><span data-stu-id="20077-260">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="20077-261">除了設定 [應用程式的基底路徑](xref:blazor/host-and-deploy/index#app-base-path)之外，還會執行移除處理常式或停用繼承。</span><span class="sxs-lookup"><span data-stu-id="20077-261">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="20077-262">將應用程式檔案中的應用程式基底路徑設定 `index.html` 為在 iis 中設定子應用程式時所使用的 iis 別名。</span><span class="sxs-lookup"><span data-stu-id="20077-262">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="20077-263">Brotli 和 Gzip 壓縮</span><span class="sxs-lookup"><span data-stu-id="20077-263">Brotli and Gzip compression</span></span>

<span data-ttu-id="20077-264">您可以透過設定 IIS `web.config` ，以提供 Brotli 或 Gzip 壓縮 Blazor 的資產。</span><span class="sxs-lookup"><span data-stu-id="20077-264">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="20077-265">如需範例設定，請參閱 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) 。</span><span class="sxs-lookup"><span data-stu-id="20077-265">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="20077-266">疑難排解</span><span class="sxs-lookup"><span data-stu-id="20077-266">Troubleshooting</span></span>

<span data-ttu-id="20077-267">如果收到「500 - 內部伺服器錯誤」\*\*，且 IIS 管理員在嘗試存取網站設定時擲回錯誤，請確認是否已安裝 URL Rewrite 模組。</span><span class="sxs-lookup"><span data-stu-id="20077-267">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="20077-268">未安裝模組時，IIS 無法剖析該檔案 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="20077-268">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="20077-269">這可防止 IIS 管理員從服務的靜態檔案載入網站的設定和網站 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="20077-269">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="20077-270">如需針對部署至 IIS 進行疑難排解的詳細資訊，請參閱 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="20077-270">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="20077-271">Azure 儲存體</span><span class="sxs-lookup"><span data-stu-id="20077-271">Azure Storage</span></span>

<span data-ttu-id="20077-272">[Azure 儲存體](/azure/storage/) 的靜態檔案裝載允許無伺服器 Blazor 應用程式裝載。</span><span class="sxs-lookup"><span data-stu-id="20077-272">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="20077-273">支援自訂網域名稱、Azure 內容傳遞網路 (CDN) 及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="20077-273">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="20077-274">當 Blob 服務針對儲存體帳戶上的靜態網站裝載啟用時：</span><span class="sxs-lookup"><span data-stu-id="20077-274">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="20077-275">將 [索引文件名稱]\*\*\*\* 設定為 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="20077-275">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="20077-276">將 [錯誤文件路徑]\*\*\*\* 設定為 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="20077-276">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="20077-277">Razor 元件和其他非檔案端點不會位於 blob 服務所儲存之靜態內容中的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="20077-277">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="20077-278">當收到路由器應處理之其中一個資源的要求時 Blazor ，blob 服務所產生的 *404-找不* 到錯誤會將要求路由傳送至 **錯誤檔路徑**。</span><span class="sxs-lookup"><span data-stu-id="20077-278">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="20077-279">`index.html`會傳回 blob，而且路由器會 Blazor 載入並處理路徑。</span><span class="sxs-lookup"><span data-stu-id="20077-279">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="20077-280">如果檔案的標頭中有不適當的 MIME 類型，則不會在執行時間載入檔案 `Content-Type` ，請採取下列其中一項動作：</span><span class="sxs-lookup"><span data-stu-id="20077-280">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="20077-281">設定您的工具，在部署檔案時) 設定正確的 MIME 類型 (`Content-Type` 標頭。</span><span class="sxs-lookup"><span data-stu-id="20077-281">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="20077-282">在部署應用程式之後，變更檔案的 MIME 類型 (`Content-Type` 標頭) 。</span><span class="sxs-lookup"><span data-stu-id="20077-282">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="20077-283">在儲存體總管 (Azure 入口網站每個檔案的) ：</span><span class="sxs-lookup"><span data-stu-id="20077-283">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="20077-284">以滑鼠右鍵按一下檔案，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="20077-284">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="20077-285">設定 **ContentType** ，然後選取 [ **儲存** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="20077-285">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="20077-286">如需詳細資訊，請參閱 [Azure 儲存體中的靜態網站裝載](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="20077-286">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="20077-287">Nginx</span><span class="sxs-lookup"><span data-stu-id="20077-287">Nginx</span></span>

<span data-ttu-id="20077-288">下列檔案 `nginx.conf` 已簡化，示範如何設定 Nginx，以便在 `index.html` 每次找不到磁片上的對應檔案時傳送檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-288">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="20077-289">使用設定 NGINX 高載 [速率限制](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) 時 [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) ， Blazor WebAssembly 應用程式可能需要大型的 `burst` 參數值，以容納應用程式所提出的相對大量要求。</span><span class="sxs-lookup"><span data-stu-id="20077-289">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="20077-290">一開始，請將值設定為至少60：</span><span class="sxs-lookup"><span data-stu-id="20077-290">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="20077-291">如果瀏覽器開發人員工具或網路流量工具表示要求收到 *503-服務無法使用* 的狀態碼，請增加值。</span><span class="sxs-lookup"><span data-stu-id="20077-291">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="20077-292">如需生產環境 Nginx 網頁伺服器組態的詳細資訊，請參閱[建立 NGINX Plus 和 NGINX 組態檔](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)。</span><span class="sxs-lookup"><span data-stu-id="20077-292">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="20077-293">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="20077-293">Nginx in Docker</span></span>

<span data-ttu-id="20077-294">若要 Blazor 使用 Nginx 在 Docker 中裝載，請設定 Dockerfile 以使用以 Alpine 為基礎的 Nginx 映射。</span><span class="sxs-lookup"><span data-stu-id="20077-294">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="20077-295">更新 Dockerfile，以將檔案複製 `nginx.config` 到容器中。</span><span class="sxs-lookup"><span data-stu-id="20077-295">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="20077-296">如下列範例所示，新增一行至 Dockerfile：</span><span class="sxs-lookup"><span data-stu-id="20077-296">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="20077-297">Apache</span><span class="sxs-lookup"><span data-stu-id="20077-297">Apache</span></span>

<span data-ttu-id="20077-298">若要將 Blazor WebAssembly 應用程式部署至 CentOS 7 或更新版本：</span><span class="sxs-lookup"><span data-stu-id="20077-298">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="20077-299">建立 Apache 設定檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-299">Create the Apache configuration file.</span></span> <span data-ttu-id="20077-300">下列範例是簡化的設定檔 (`blazorapp.config`) ：</span><span class="sxs-lookup"><span data-stu-id="20077-300">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="20077-301">將 Apache 設定檔放入 `/etc/httpd/conf.d/` 目錄中，這是 CentOS 7 中的預設 Apache 設定目錄。</span><span class="sxs-lookup"><span data-stu-id="20077-301">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="20077-302">將應用程式的檔案放入 `/var/www/blazorapp` 目錄中， (`DocumentRoot` 設定檔) 中指定的位置。</span><span class="sxs-lookup"><span data-stu-id="20077-302">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="20077-303">重新開機 Apache 服務。</span><span class="sxs-lookup"><span data-stu-id="20077-303">Restart the Apache service.</span></span>

<span data-ttu-id="20077-304">如需詳細資訊，請參閱 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="20077-304">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="20077-305">GitHub 頁面</span><span class="sxs-lookup"><span data-stu-id="20077-305">GitHub Pages</span></span>

<span data-ttu-id="20077-306">若要處理 URL 重寫，請新增具有腳本的檔案，以處理將要求重新導向 `wwwroot/404.html` 至頁面的腳本 `index.html` 。</span><span class="sxs-lookup"><span data-stu-id="20077-306">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="20077-307">如需範例，請參閱 [SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)：</span><span class="sxs-lookup"><span data-stu-id="20077-307">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="20077-308">[即時網站](https://stevesandersonms.github.io/BlazorOnGitHubPages/)) </span><span class="sxs-lookup"><span data-stu-id="20077-308">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="20077-309">使用專案網站而非組織網站時，請 `<base>` 在中更新標記 `wwwroot/index.html` 。</span><span class="sxs-lookup"><span data-stu-id="20077-309">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="20077-310">`href`使用尾端斜線將屬性值設定為 GitHub 存放庫名稱 (例如 `/my-repository/`) 。</span><span class="sxs-lookup"><span data-stu-id="20077-310">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="20077-311">在[SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)中， `href` [ `.github/workflows/main.yml` 設定檔](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)會在發佈時更新基底。</span><span class="sxs-lookup"><span data-stu-id="20077-311">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="20077-312">[SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)並非由 .Net Foundation 或 Microsoft 所擁有、維護或支援。</span><span class="sxs-lookup"><span data-stu-id="20077-312">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="20077-313">主機組態值</span><span class="sxs-lookup"><span data-stu-id="20077-313">Host configuration values</span></span>

<span data-ttu-id="20077-314">在開發環境中， [ Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)可以在執行時間接受下列主機配置值作為命令列引數。</span><span class="sxs-lookup"><span data-stu-id="20077-314">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="20077-315">內容根目錄</span><span class="sxs-lookup"><span data-stu-id="20077-315">Content root</span></span>

<span data-ttu-id="20077-316">`--contentroot`引數會將包含應用程式內容檔案的目錄絕對路徑設定 ([內容根目錄](xref:fundamentals/index#content-root)) 。</span><span class="sxs-lookup"><span data-stu-id="20077-316">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="20077-317">在下列範例中，`/content-root-path` 是應用程式的內容根路徑。</span><span class="sxs-lookup"><span data-stu-id="20077-317">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="20077-318">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="20077-318">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="20077-319">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="20077-319">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="20077-320">`launchSettings.json`在**IIS Express**設定檔中，將專案新增至應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-320">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="20077-321">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="20077-321">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="20077-322">在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數**中指定引數。</span><span class="sxs-lookup"><span data-stu-id="20077-322">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="20077-323">在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="20077-323">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="20077-324">路徑基底</span><span class="sxs-lookup"><span data-stu-id="20077-324">Path base</span></span>

<span data-ttu-id="20077-325">`--pathbase`引數會將應用程式的應用程式基底路徑設定為使用非根相對 URL 路徑在本機執行 (將 `<base>` `href` 標籤設定為 `/` 暫存和生產) 以外的路徑。</span><span class="sxs-lookup"><span data-stu-id="20077-325">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="20077-326">在下列範例中，`/relative-URL-path` 是應用程式的路徑基底。</span><span class="sxs-lookup"><span data-stu-id="20077-326">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="20077-327">如需詳細資訊，請參閱 [應用程式基底路徑](xref:blazor/host-and-deploy/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="20077-327">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="20077-328">不同於提供給 `<base>` 標籤 `href` 的路徑，在傳遞 `--pathbase` 引數值時請勿包含尾端的斜線 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="20077-328">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="20077-329">如果應用程式基底路徑提供於 `<base>` 標籤作為 `<base href="/CoolApp/">` (包括尾端的斜線)，請將命令列引數值傳遞為 `--pathbase=/CoolApp` (不含尾端的斜線)。</span><span class="sxs-lookup"><span data-stu-id="20077-329">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="20077-330">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="20077-330">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="20077-331">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="20077-331">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="20077-332">`launchSettings.json`在**IIS Express**設定檔中，將專案新增至應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-332">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="20077-333">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="20077-333">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="20077-334">在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數**中指定引數。</span><span class="sxs-lookup"><span data-stu-id="20077-334">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="20077-335">在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="20077-335">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="20077-336">URL</span><span class="sxs-lookup"><span data-stu-id="20077-336">URLs</span></span>

<span data-ttu-id="20077-337">`--urls` 引數會搭配要用來接聽要求的連接埠和通訊協定，設定 IP 位址或主機位址。</span><span class="sxs-lookup"><span data-stu-id="20077-337">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="20077-338">在命令提示字元，於本機執行應用程式時傳遞引數。</span><span class="sxs-lookup"><span data-stu-id="20077-338">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="20077-339">從應用程式目錄，執行：</span><span class="sxs-lookup"><span data-stu-id="20077-339">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="20077-340">`launchSettings.json`在**IIS Express**設定檔中，將專案新增至應用程式的檔案。</span><span class="sxs-lookup"><span data-stu-id="20077-340">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="20077-341">此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。</span><span class="sxs-lookup"><span data-stu-id="20077-341">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="20077-342">在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數**中指定引數。</span><span class="sxs-lookup"><span data-stu-id="20077-342">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="20077-343">在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="20077-343">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="20077-344">設定連結器</span><span class="sxs-lookup"><span data-stu-id="20077-344">Configure the Linker</span></span>

<span data-ttu-id="20077-345">Blazor 在每個發行組建上執行中繼語言 (IL) 連結，以從輸出元件中移除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="20077-345">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="20077-346">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="20077-346">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="20077-347">自訂開機資源載入</span><span class="sxs-lookup"><span data-stu-id="20077-347">Custom boot resource loading</span></span>

<span data-ttu-id="20077-348">您 Blazor WebAssembly 可以使用函式來初始化應用程式 `loadBootResource` ，以覆寫內建的開機資源載入機制。</span><span class="sxs-lookup"><span data-stu-id="20077-348">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="20077-349">用於 `loadBootResource` 下列案例：</span><span class="sxs-lookup"><span data-stu-id="20077-349">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="20077-350">允許使用者載入靜態資源，例如時區資料或 `dotnet.wasm` CDN。</span><span class="sxs-lookup"><span data-stu-id="20077-350">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="20077-351">使用 HTTP 要求載入壓縮的元件，並在不支援從伺服器提取壓縮內容的主機用戶端上解壓縮這些元件。</span><span class="sxs-lookup"><span data-stu-id="20077-351">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="20077-352">將每個要求重新導向至新名稱，將別名資源重新導向至不同的名稱 `fetch` 。</span><span class="sxs-lookup"><span data-stu-id="20077-352">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="20077-353">`loadBootResource` 參數會出現在下表中。</span><span class="sxs-lookup"><span data-stu-id="20077-353">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="20077-354">參數</span><span class="sxs-lookup"><span data-stu-id="20077-354">Parameter</span></span>    | <span data-ttu-id="20077-355">描述</span><span class="sxs-lookup"><span data-stu-id="20077-355">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="20077-356">資源類型。</span><span class="sxs-lookup"><span data-stu-id="20077-356">The type of the resource.</span></span> <span data-ttu-id="20077-357">運算子類型： `assembly` 、 `pdb` 、 `dotnetjs` 、 `dotnetwasm` 、 `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="20077-357">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="20077-358">資源名稱。</span><span class="sxs-lookup"><span data-stu-id="20077-358">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="20077-359">資源的相對或絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="20077-359">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="20077-360">代表回應中預期內容的完整性字串。</span><span class="sxs-lookup"><span data-stu-id="20077-360">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="20077-361">`loadBootResource` 傳回下列任一項以覆寫載入進程：</span><span class="sxs-lookup"><span data-stu-id="20077-361">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="20077-362">URI 字串。</span><span class="sxs-lookup"><span data-stu-id="20077-362">URI string.</span></span> <span data-ttu-id="20077-363">在下列範例 (`wwwroot/index.html`) 中，從 CDN 提供下列檔案 `https://my-awesome-cdn.com/` ：</span><span class="sxs-lookup"><span data-stu-id="20077-363">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="20077-364">時區資料</span><span class="sxs-lookup"><span data-stu-id="20077-364">Timezone data</span></span>

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

* <span data-ttu-id="20077-365">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="20077-365">`Promise<Response>`.</span></span> <span data-ttu-id="20077-366">`integrity`在標頭中傳遞參數，以保留預設的完整性檢查行為。</span><span class="sxs-lookup"><span data-stu-id="20077-366">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="20077-367">下列範例 (`wwwroot/index.html`) 將自訂 HTTP 標頭新增至輸出要求，並將 `integrity` 參數傳遞至 `fetch` 呼叫：</span><span class="sxs-lookup"><span data-stu-id="20077-367">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="20077-368">`null`/`undefined`，這會導致預設的載入行為。</span><span class="sxs-lookup"><span data-stu-id="20077-368">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="20077-369">外部來源必須傳回瀏覽器所需的 CORS 標頭，以允許跨原始來源資源載入。</span><span class="sxs-lookup"><span data-stu-id="20077-369">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="20077-370">依預設，Cdn 通常會提供必要的標頭。</span><span class="sxs-lookup"><span data-stu-id="20077-370">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="20077-371">您只需要指定自訂行為的類型。</span><span class="sxs-lookup"><span data-stu-id="20077-371">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="20077-372">未指定的類型 `loadBootResource` 會根據其預設載入行為載入架構。</span><span class="sxs-lookup"><span data-stu-id="20077-372">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="20077-373">變更 DLL 檔案的副檔名</span><span class="sxs-lookup"><span data-stu-id="20077-373">Change the filename extension of DLL files</span></span>

<span data-ttu-id="20077-374">如果您需要變更應用程式已發佈檔案的副檔名 `.dll` ，請遵循本節中的指導方針。</span><span class="sxs-lookup"><span data-stu-id="20077-374">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="20077-375">發佈應用程式之後，請使用 shell 腳本或 DevOps 組建管線，將檔案重新命名 `.dll` 以使用不同的副檔名。</span><span class="sxs-lookup"><span data-stu-id="20077-375">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="20077-376">以 `.dll` `wwwroot` 應用程式發佈輸出的目錄中的檔案為目標 (例如 `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 。</span><span class="sxs-lookup"><span data-stu-id="20077-376">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="20077-377">在下列範例中， `.dll` 會將檔案重新命名為使用 `.bin` 副檔名。</span><span class="sxs-lookup"><span data-stu-id="20077-377">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="20077-378">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="20077-378">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="20077-379">如果服務工作者資產也在使用中，請新增下列命令：</span><span class="sxs-lookup"><span data-stu-id="20077-379">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="20077-380">在 Linux 或 macOS 上：</span><span class="sxs-lookup"><span data-stu-id="20077-380">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="20077-381">如果服務工作者資產也在使用中，請新增下列命令：</span><span class="sxs-lookup"><span data-stu-id="20077-381">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="20077-382">若要使用不同的副檔名 `.bin` ，請 `.bin` 在上述命令中取代。</span><span class="sxs-lookup"><span data-stu-id="20077-382">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="20077-383">若要處理壓縮 `blazor.boot.json.gz` 和檔案 `blazor.boot.json.br` ，請採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="20077-383">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="20077-384">移除壓縮的 `blazor.boot.json.gz` 和檔案 `blazor.boot.json.br` 。</span><span class="sxs-lookup"><span data-stu-id="20077-384">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="20077-385">這種方法會停用壓縮。</span><span class="sxs-lookup"><span data-stu-id="20077-385">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="20077-386">重新壓縮更新後的檔案 `blazor.boot.json` 。</span><span class="sxs-lookup"><span data-stu-id="20077-386">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="20077-387">先前的指導方針也適用于服務工作者資產正在使用中。</span><span class="sxs-lookup"><span data-stu-id="20077-387">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="20077-388">移除或重新壓縮 `wwwroot/service-worker-assets.js.br` 和 `wwwroot/service-worker-assets.js.gz` 。</span><span class="sxs-lookup"><span data-stu-id="20077-388">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="20077-389">否則，瀏覽器中的檔案完整性檢查會失敗。</span><span class="sxs-lookup"><span data-stu-id="20077-389">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="20077-390">下列 Windows 範例使用放置於專案根目錄的 PowerShell 腳本。</span><span class="sxs-lookup"><span data-stu-id="20077-390">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="20077-391">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="20077-391">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="20077-392">如果服務工作者資產也在使用中，請新增下列命令：</span><span class="sxs-lookup"><span data-stu-id="20077-392">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="20077-393">在專案檔中，腳本會在發佈應用程式之後執行：</span><span class="sxs-lookup"><span data-stu-id="20077-393">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="20077-394">重新命名和消極式載入相同的元件時，請參閱中的指導方針 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> 。</span><span class="sxs-lookup"><span data-stu-id="20077-394">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

<span data-ttu-id="20077-395">若要提供意見反應，請造訪 [aspnetcore/問題 #5477](https://github.com/dotnet/aspnetcore/issues/5477)。</span><span class="sxs-lookup"><span data-stu-id="20077-395">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
