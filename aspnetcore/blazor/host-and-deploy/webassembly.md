---
title: 裝載和部署 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何 Blazor 使用 ASP.NET Core、內容傳遞網路 (CDN) 、檔案伺服器和 GitHub 頁面來裝載和部署應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
ms.openlocfilehash: 5983cbc1e0256f7cf8e85fb07f9ba1bbc1bf08db
ms.sourcegitcommit: c321518bfe367280ef262aecaada287f17fe1bc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011867"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>裝載和部署 ASP.NET Core Blazor WebAssembly

[Luke Latham](https://github.com/guardrex)、 [Rainer Stropek](https://www.timecockpit.com)、 [Daniel Roth](https://github.com/danroth27)、 [Ben Adams](https://twitter.com/ben_a_adams)及[Safia Abdalla](https://safia.rocks)

使用[ Blazor WebAssembly 裝載模型](xref:blazor/hosting-models#blazor-webassembly)：

* Blazor應用程式、其相依性和 .net 執行時間會以平行方式下載至瀏覽器。
* 應用程式會直接在瀏覽器 UI 執行緒上執行。

以下是支援的部署策略：

* Blazor應用程式是由 ASP.NET Core 應用程式提供服務。 此策略已於[搭配 ASP.NET Core 的已裝載部署](#hosted-deployment-with-aspnet-core)一節中涵蓋。
* Blazor應用程式會放置在靜態裝載 web 伺服器或服務上，而不會使用 .net 來提供 Blazor 應用程式。 此策略包含在 [獨立部署](#standalone-deployment) 區段中，其中包含將 Blazor WebAssembly 應用程式裝載為 IIS 子應用程式的相關資訊。

## <a name="compression"></a>壓縮

Blazor WebAssembly發佈應用程式時，會在發行期間以靜態方式壓縮輸出，以減少應用程式的大小，並移除執行時間壓縮的額外負荷。 使用的壓縮演算法如下：

* [Brotli](https://tools.ietf.org/html/rfc7932) (最高層級) 
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor 依賴主機提供適當的壓縮檔案。 使用 ASP.NET Core 裝載的專案時，主專案可以執行內容協商以及提供靜態壓縮檔案。 裝載 Blazor WebAssembly 獨立應用程式時，可能需要額外的工作，以確保提供靜態壓縮檔案：

* 如需 IIS `web.config` 壓縮設定，請參閱 [Iis： Brotli 和 Gzip 壓縮](#brotli-and-gzip-compression) 一節。 
* 裝載在不支援靜態壓縮的檔案內容協商的靜態裝載方案（例如 GitHub 頁面）時，請考慮將應用程式設定為提取和解碼 Brotli 壓縮檔案：

  * 從 [google/Brotli GitHub 存放庫](https://github.com/google/brotli)取得 JavaScript Brotli 解碼器。 從2020年9月起， `decode.js` 系統會在存放庫的[ `js` 資料夾](https://github.com/google/brotli/tree/master/js)中命名並重命名的解碼器檔案。
  
    > [!NOTE]
    > 縮減版本的 `decode.js` 腳本 (`decode.min.js`) [Google/brotli GitHub 存放庫](https://github.com/google/brotli)中有回歸。 您可以自行縮短腳本，或使用 [npm 套件](https://www.npmjs.com/package/brotli) ，直到問題時段為止 [。 BrotliDecode 未設定于 decode.min.js (google/brotli #844) ](https://github.com/google/brotli/issues/844) 已解決。 本節中的範例程式碼會使用腳本的 **unminified** 版本。

  * 更新應用程式以使用此解碼器。 將結束記號內的標記變更 `<body>` `wwwroot/index.html` 為下列內容：
  
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
 
若要停用壓縮，請在 `BlazorEnableCompression` 應用程式的專案檔中新增 MSBuild 屬性，並將值設定為 `false` ：

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

您 `BlazorEnableCompression` 可以 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 在命令 shell 中使用下列語法，將屬性傳遞至命令：

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>重寫 URL 以便正確地路由

應用程式中頁面元件的路由要求 Blazor WebAssembly ，與裝載應用程式中的路由要求一樣簡單 Blazor Server 。 請考慮 Blazor WebAssembly 具有兩個元件的應用程式：

* `Main.razor`：在應用程式的根目錄載入，並且包含 `About` 元件 () 的連結 `href="About"` 。
* `About.razor`： `About` component。

使用瀏覽器的網址列要求應用程式預設文件時 (例如 `https://www.contoso.com/`)：

1. 瀏覽器提出要求。
1. 預設頁面會傳回，通常是 `index.html` 。
1. `index.html` 啟動應用程式。
1. Blazor的路由器會載入，而 Razor `Main` 元件則會呈現。

在主頁面中，選取元件的連結可 `About` 在用戶端上運作，因為 Blazor 路由器會停止瀏覽器在網際網路上提出要求， `www.contoso.com` 並提供轉譯 `About` 的 `About` 元件本身。 *Blazor WebAssembly 應用程式內* 內部端點的所有要求運作方式相同：要求不會對網際網路上伺服器裝載的資源觸發以瀏覽器為基礎的要求。 路由器會在內部處理要求。

如果使用瀏覽器之網址列提出對 `www.contoso.com/About` 的要求，則要求會失敗。 在應用程式的網際網路主機上沒有這類資源存在，因此會傳回「404 - 找不到」的回應。

因為瀏覽器會對以網際網路為基礎的主機發出要求，以提供用戶端頁面，所以 web 伺服器和主機服務必須將所有不在伺服器上的資源要求重寫為 `index.html` 頁面。 當 `index.html` 傳回時，應用程式的 Blazor 路由器會接管並回應正確的資源。

部署至 IIS 伺服器時，您可以使用 URL 重寫模組與應用程式的已發佈檔案 `web.config` 。 如需詳細資訊，請參閱 [IIS](#iis) 一節。

## <a name="hosted-deployment-with-aspnet-core"></a>搭配 ASP.NET Core 的已裝載部署

*託管部署* Blazor WebAssembly 可從 web 伺服器上執行的 [ASP.NET Core 應用程式](xref:index)，將應用程式提供給瀏覽器。

用戶端 Blazor WebAssembly 應用程式會 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 與伺服器應用程式的任何其他靜態 web 資產一起發行至伺服器應用程式的資料夾中。 這兩個應用程式會一起部署。 需要有能夠裝載 ASP.NET Core 應用程式的網頁伺服器。 針對裝載的部署，當使用命令) 時，Visual Studio 包含 **Blazor WebAssembly 應用程式** 專案範本 (`blazorwasm` 範本， [`dotnet new`](/dotnet/core/tools/dotnet-new) **`Hosted`** (`-ho|--hosted` 使用命令) 時所選取的選項 `dotnet new` 。

如需 ASP.NET Core 應用程式裝載和部署的詳細資訊，請參閱 <xref:host-and-deploy/index>。

如需部署至 Azure App Service 的相關資訊，請參閱 <xref:tutorials/publish-to-azure-webapp-using-vs>。

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>具有多個應用程式的託管部署 Blazor WebAssembly

### <a name="app-configuration"></a>應用程式組態

若要設定託管 Blazor 解決方案來提供多個 Blazor WebAssembly 應用程式服務：

* 使用現有的主控 Blazor 方案，或從裝載的專案範本建立新的方案 Blazor 。

* 在用戶端應用程式的專案檔中，將屬性新增至，並以的 `<StaticWebAssetBasePath>` `<PropertyGroup>` 值 `FirstApp` 設定專案靜態資產的基底路徑：

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* 將第二個用戶端應用程式新增至解決方案：

  * 將名為的資料夾加入 `SecondClient` 方案的資料夾中。
  * 在 Blazor WebAssembly `SecondBlazorApp.Client` 專案範本的資料夾中，建立名為的應用程式 `SecondClient` Blazor WebAssembly 。
  * 在應用程式的專案檔中：

    * 將屬性新增至，其 `<StaticWebAssetBasePath>` 值為 `<PropertyGroup>` `SecondApp` ：

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * 將專案參考新增至 `Shared` 專案：

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      預留位置 `{SOLUTION NAME}` 是解決方案的名稱。

* 在伺服器應用程式的專案檔中，為新增的用戶端應用程式建立專案參考：

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* 在伺服器應用程式的 `Properties/launchSettings.json` 檔案中，設定 `applicationUrl` Kestrel 設定檔 () 的， `{SOLUTION NAME}.Server` 以存取埠5001和5002上的用戶端應用程式：

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* 在伺服器應用程式的 `Startup.Configure` 方法 (`Startup.cs`) 中，移除下列幾行，這些行會在呼叫之後出現 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ：

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

  新增將要求對應至用戶端應用程式的中介軟體。 下列範例會將中介軟體設定為在下列情況下執行：

  * 針對已新增的用戶端應用程式，要求埠可能是5001（適用于原始用戶端應用程式）或5002。
  * 要求主機 `firstapp.com` 適用于原始用戶端應用程式或 `secondapp.com` 新增的用戶端應用程式。

    > [!NOTE]
    > 本節所示的範例需要下列各項的額外設定：
    >
    > * 存取範例主機網域、和上的應用 `firstapp.com` 程式 `secondapp.com` 。
    > * 用戶端應用程式用來啟用 TLS 安全性的憑證 (HTTPS) 。
    >
    > 必要的設定已超出本文的範圍，並取決於裝載解決方案的方式。 如需詳細資訊，請參閱 [主機和部署文章](xref:host-and-deploy/index)。

  將下列程式碼放在先前移除的行：

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

* 在伺服器應用程式的氣象預報控制器 (`Controllers/WeatherForecastController.cs`) 中，將現有的路由 (`[Route("[controller]")]`) 取代為 `WeatherForecastController` 下列路由：

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  先前新增至伺服器應用程式方法的中介軟體會 `Startup.Configure` `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` 根據埠 (5001/5002) 或網域 (`firstapp.com` / `secondapp.com`) ，將連入要求修改至或。 需要上述的控制器路由，才能將天氣資料從伺服器應用程式傳回給用戶端應用程式。

### <a name="static-assets-and-class-libraries"></a>靜態資產和類別庫

針對靜態資產，請使用下列方法：

* 當資產位於用戶端應用程式的 `wwwroot` 資料夾時，請正常提供其路徑：

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* 當資產位於 `wwwroot` 類別庫的資料夾中時[ Razor (RCL) ](xref:blazor/components/class-libraries)，請依[RCL 文章](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)中的指導方針參考用戶端應用程式中的靜態資產：

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

類別庫提供給用戶端應用程式的元件會正常地參考。 如果有任何元件需要樣式表單或 JavaScript 檔案，用戶端應用程式的檔案 `wwwroot/index.html` 必須包含正確的靜態資產連結。 下列範例會示範這些方法。

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

將下列 `Jeep` 元件新增至其中一個用戶端應用程式。 `Jeep`元件使用：

* 從用戶端應用程式的 `wwwroot` 資料夾 () 的映射 `jeep-cj.png` 。
* 新增的元件連結 [ Razor 庫](xref:blazor/components/class-libraries) 中的映射 (`JeepImage`) `wwwroot` 資料夾 (`jeep-yj.png`) 。
* `Component1`當連結 `JeepImage` 庫加入至方案時，RCL 專案範本會自動建立範例元件 () 。

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
> 除非您擁有映射， **否則請勿公開發布車輛** 的影像。 否則，您會面臨著作權侵權的風險。

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

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

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

程式庫的 `jeep-yj.png` 映射也可以新增至程式庫的 `Component1` 元件 (`Component1.razor`) ：

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

用戶端應用程式的檔案會 `wwwroot/index.html` 以下列新增的標記要求程式庫的樣式表單 `<link>` ：

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

將流覽新增至 `Jeep` 用戶端應用程式元件中的元件 `NavMenu` (`Shared/NavMenu.razor`) ：

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

如需 RCLs 的詳細資訊，請參閱：

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>獨立部署

*獨立部署* Blazor WebAssembly 會以一組直接由用戶端要求的靜態檔案來提供應用程式。 任何靜態檔案伺服器都能提供 Blazor 應用程式。

獨立部署資產會發佈到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 資料夾。

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly 您可以將應用程式部署至 Windows 上的 Azure App 服務，這會在 [IIS](#iis)上裝載應用程式。

Blazor WebAssembly目前不支援將獨立應用程式部署至適用于 Linux 的 Azure App Service。 目前無法使用 Linux 伺服器映射來裝載應用程式。 正在進行工作，以啟用此案例。

### <a name="iis"></a>IIS

IIS 是適用于應用程式的可用靜態檔案伺服器 Blazor 。 若要設定 IIS 以裝載 Blazor ，請參閱 [在 Iis 上建立靜態網站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。

已發佈的資產會在 `/bin/Release/{TARGET FRAMEWORK}/publish` 資料夾中建立。 在 `publish` web 伺服器或主機服務上裝載資料夾的內容。

#### <a name="webconfig"></a>web.config

Blazor發行專案時， `web.config` 會使用下列 IIS 設定來建立檔案：

* 針對下列副檔名設定 MIME 類型：
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* 針對下列 MIME 類型會啟用 HTTP 壓縮：
  * `application/octet-stream`
  * `application/wasm`
* 建立 URL Rewrite Module 規則：
  * 提供應用程式的靜態資產位於 () 的子目錄 `wwwroot/{PATH REQUESTED}` 。
  * 建立 SPA 回路由，讓非檔案資產的要求重新導向至其靜態資產資料夾中的應用程式預設檔 (`wwwroot/index.html`) 。
  
#### <a name="use-a-custom-webconfig"></a>使用自訂 web.config

若要使用自訂檔案 `web.config` ，請將自訂檔案放 `web.config` 在專案資料夾的根目錄。 使用在應用程式的專案檔中，將專案設定為發佈 IIS 特定的資產 `PublishIISAssets` ，併發布專案：

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a>安裝 URL Rewrite 模組

需要 [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite)，才可重寫 URL。 預設不會安裝此模組，且其無法用來安裝為網頁伺服器 (IIS) 角色服務功能。 必須從 IIS 網站下載模組。 請使用 Web Platform Installer 安裝模組：

1. 在本機上，巡覽至 [URL Rewrite Module 下載頁面](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。 如需英文版，請選取 [WebPI] 下載 WebPI 安裝程式。 如需其他語言，請選取適當的伺服器架構 (x86 x64) 來下載安裝程式。
1. 將安裝程式複製到伺服器。 執行安裝程式。 選取 [安裝] 按鈕，並接受授權條款。 安裝完成之後，不需要重新啟動伺服器。

#### <a name="configure-the-website"></a>設定網站

將網站的 [實體路徑] 設為應用程式的資料夾。 此資料夾包含：

* `web.config`IIS 用來設定網站的檔案，包括必要的重新導向規則和檔案內容類型。
* 應用程式的靜態資產資料夾。

#### <a name="host-as-an-iis-sub-app"></a>作為 IIS 子應用程式的主機

如果獨立應用程式裝載為 IIS 子應用程式，請執行下列其中一項：

* 停用繼承的 ASP.NET Core 模組處理常式。

  Blazor `web.config` 將區段新增至檔案，以移除應用程式已發佈檔案中的處理常式 `<handlers>` ：

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `<system.webServer>`使用 `<location>` `inheritInChildApplications` 設定為下列專案的元素，停用根 (父) 應用程式區段的繼承 `false` ：

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

除了設定 [應用程式的基底路徑](xref:blazor/host-and-deploy/index#app-base-path)之外，還會執行移除處理常式或停用繼承。 將應用程式檔案中的應用程式基底路徑設定 `index.html` 為在 iis 中設定子應用程式時所使用的 iis 別名。

#### <a name="brotli-and-gzip-compression"></a>Brotli 和 Gzip 壓縮

*本節僅適用于獨立的 Blazor WebAssembly 應用程式。裝載 Blazor 的應用程式會使用預設的 ASP.NET Core 應用程式 `web.config` 檔，而不是本節所連結的檔案。*

您可以透過設定 IIS `web.config` ，為獨立應用程式提供 Brotli 或 Gzip 壓縮 Blazor 的資產 Blazor WebAssembly 。 如需範例設定檔，請參閱 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) 。

在下列情況下，可能需要額外的範例檔案設定 `web.config` ：

* 應用程式的規格會呼叫下列其中一項：
  * 提供範例檔案未設定的壓縮檔案 `web.config` 。
  * 以未壓縮的格式提供範例檔案所設定的壓縮檔案 `web.config` 。
* 伺服器的 IIS 設定 (例如， `applicationHost.config`) 提供伺服器層級的 iis 預設值。 根據伺服器層級的設定，應用程式可能需要與範例檔案所包含的 IIS 設定不同 `web.config` 。

#### <a name="troubleshooting"></a>疑難排解

如果收到「500 - 內部伺服器錯誤」，且 IIS 管理員在嘗試存取網站設定時擲回錯誤，請確認是否已安裝 URL Rewrite 模組。 未安裝模組時，IIS 無法剖析該檔案 `web.config` 。 這可防止 IIS 管理員從服務的靜態檔案載入網站的設定和網站 Blazor 。

如需針對部署至 IIS 進行疑難排解的詳細資訊，請參閱 <xref:test/troubleshoot-azure-iis>。

### <a name="azure-storage"></a>Azure 儲存體

[Azure 儲存體](/azure/storage/) 的靜態檔案裝載允許無伺服器 Blazor 應用程式裝載。 支援自訂網域名稱、Azure 內容傳遞網路 (CDN) 及 HTTPS。

當 Blob 服務針對儲存體帳戶上的靜態網站裝載啟用時：

* 將 [索引文件名稱] 設定為 `index.html`。
* 將 [錯誤文件路徑] 設定為 `index.html`。 Razor 元件和其他非檔案端點不會位於 blob 服務所儲存之靜態內容中的實體路徑。 當收到路由器應處理之其中一個資源的要求時 Blazor ，blob 服務所產生的 *404-找不* 到錯誤會將要求路由傳送至 **錯誤檔路徑**。 `index.html`會傳回 blob，而且路由器會 Blazor 載入並處理路徑。

如果檔案的標頭中有不適當的 MIME 類型，則不會在執行時間載入檔案 `Content-Type` ，請採取下列其中一項動作：

* 設定您的工具，在部署檔案時) 設定正確的 MIME 類型 (`Content-Type` 標頭。
* 在部署應用程式之後，變更檔案的 MIME 類型 (`Content-Type` 標頭) 。

  在儲存體總管 (Azure 入口網站每個檔案的) ：
  
  1. 以滑鼠右鍵按一下檔案，然後選取 [ **屬性**]。
  1. 設定 **ContentType** ，然後選取 [ **儲存** ] 按鈕。

如需詳細資訊，請參閱 [Azure 儲存體中的靜態網站裝載](/azure/storage/blobs/storage-blob-static-website)。

### <a name="nginx"></a>Nginx

下列檔案 `nginx.conf` 已簡化，示範如何設定 Nginx，以便在 `index.html` 每次找不到磁片上的對應檔案時傳送檔案。

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

使用設定 NGINX 高載 [速率限制](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) 時 [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) ， Blazor WebAssembly 應用程式可能需要大型的 `burst` 參數值，以容納應用程式所提出的相對大量要求。 一開始，請將值設定為至少60：

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

如果瀏覽器開發人員工具或網路流量工具表示要求收到 *503-服務無法使用* 的狀態碼，請增加值。

如需生產環境 Nginx 網頁伺服器組態的詳細資訊，請參閱[建立 NGINX Plus 和 NGINX 組態檔](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)。

### <a name="nginx-in-docker"></a>Docker 中的 Nginx

若要 Blazor 使用 Nginx 在 Docker 中裝載，請設定 Dockerfile 以使用以 Alpine 為基礎的 Nginx 映射。 更新 Dockerfile，以將檔案複製 `nginx.config` 到容器中。

如下列範例所示，新增一行至 Dockerfile：

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

若要將 Blazor WebAssembly 應用程式部署至 CentOS 7 或更新版本：

1. 建立 Apache 設定檔案。 下列範例是簡化的設定檔 (`blazorapp.config`) ：

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

1. 將 Apache 設定檔放入 `/etc/httpd/conf.d/` 目錄中，這是 CentOS 7 中的預設 Apache 設定目錄。

1. 將應用程式的檔案放入 `/var/www/blazorapp` 目錄中， (`DocumentRoot` 設定檔) 中指定的位置。

1. 重新開機 Apache 服務。

如需詳細資訊，請參閱 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。

### <a name="github-pages"></a>GitHub 頁面

若要處理 URL 重寫，請新增具有腳本的檔案，以處理將要求重新導向 `wwwroot/404.html` 至頁面的腳本 `index.html` 。 如需範例，請參閱 [SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)：

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [即時網站](https://stevesandersonms.github.io/BlazorOnGitHubPages/)) 

使用專案網站而非組織網站時，請 `<base>` 在中更新標記 `wwwroot/index.html` 。 `href`使用尾端斜線將屬性值設定為 GitHub 存放庫名稱 (例如 `/my-repository/`) 。 在[SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)中， `href` [ `.github/workflows/main.yml` 設定檔](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)會在發佈時更新基底。

> [!NOTE]
> [SteveSandersonMS/ Blazor OnGitHubPages GitHub 存放庫](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)並非由 .Net Foundation 或 Microsoft 所擁有、維護或支援。

## <a name="host-configuration-values"></a>主機組態值

在開發環境中， [ Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)可以在執行時間接受下列主機配置值作為命令列引數。

### <a name="content-root"></a>內容根目錄

`--contentroot`引數會將包含應用程式內容檔案的目錄絕對路徑設定 ([內容根目錄](xref:fundamentals/index#content-root)) 。 在下列範例中，`/content-root-path` 是應用程式的內容根路徑。

* 在命令提示字元，於本機執行應用程式時傳遞引數。 從應用程式目錄，執行：

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* `launchSettings.json`在 **IIS Express** 設定檔中，將專案新增至應用程式的檔案。 此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* 在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數** 中指定引數。 在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>路徑基底

`--pathbase`引數會將應用程式的應用程式基底路徑設定為使用非根相對 URL 路徑在本機執行 (將 `<base>` `href` 標籤設定為 `/` 暫存和生產) 以外的路徑。 在下列範例中，`/relative-URL-path` 是應用程式的路徑基底。 如需詳細資訊，請參閱 [應用程式基底路徑](xref:blazor/host-and-deploy/index#app-base-path)。

> [!IMPORTANT]
> 不同於提供給 `<base>` 標籤 `href` 的路徑，在傳遞 `--pathbase` 引數值時請勿包含尾端的斜線 (`/`)。 如果應用程式基底路徑提供於 `<base>` 標籤作為 `<base href="/CoolApp/">` (包括尾端的斜線)，請將命令列引數值傳遞為 `--pathbase=/CoolApp` (不含尾端的斜線)。

* 在命令提示字元，於本機執行應用程式時傳遞引數。 從應用程式目錄，執行：

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* `launchSettings.json`在 **IIS Express** 設定檔中，將專案新增至應用程式的檔案。 此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* 在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數** 中指定引數。 在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

`--urls` 引數會搭配要用來接聽要求的連接埠和通訊協定，設定 IP 位址或主機位址。

* 在命令提示字元，於本機執行應用程式時傳遞引數。 從應用程式目錄，執行：

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* `launchSettings.json`在 **IIS Express** 設定檔中，將專案新增至應用程式的檔案。 此設定的使用時機為當應用程式是搭配 Visual Studio 偵錯工具並以 `dotnet run` 從命令提示字元執行的情況。

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* 在 Visual Studio 中，于 [**屬性**]  >  **調試**  >  **程式引數** 中指定引數。 在 [Visual Studio] 屬性頁中設定引數，會將引數新增至檔案 `launchSettings.json` 。

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>設定修剪器

Blazor 在每個發行組建上執行中繼語言 (IL) 修剪，以從輸出元件中移除不必要的 IL。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-trimmer>。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>設定連結器

Blazor 在每個發行組建上執行中繼語言 (IL) 連結，以從輸出元件中移除不必要的 IL。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/configure-linker>。

::: moniker-end

## <a name="custom-boot-resource-loading"></a>自訂開機資源載入

您 Blazor WebAssembly 可以使用函式來初始化應用程式 `loadBootResource` ，以覆寫內建的開機資源載入機制。 用於 `loadBootResource` 下列案例：

* 允許使用者載入靜態資源，例如時區資料或 `dotnet.wasm` CDN。
* 使用 HTTP 要求載入壓縮的元件，並在不支援從伺服器提取壓縮內容的主機用戶端上解壓縮這些元件。
* 將每個要求重新導向至新名稱，將別名資源重新導向至不同的名稱 `fetch` 。

`loadBootResource` 參數會出現在下表中。

| 參數    | 描述 |
| ------------ | ----------- |
| `type`       | 資源類型。 運算子類型： `assembly` 、 `pdb` 、 `dotnetjs` 、 `dotnetwasm` 、 `timezonedata` |
| `name`       | 資源名稱。 |
| `defaultUri` | 資源的相對或絕對 URI。 |
| `integrity`  | 代表回應中預期內容的完整性字串。 |

`loadBootResource` 傳回下列任一項以覆寫載入進程：

* URI 字串。 在下列範例 (`wwwroot/index.html`) 中，從 CDN 提供下列檔案 `https://my-awesome-cdn.com/` ：

  * `dotnet.*.js`
  * `dotnet.wasm`
  * 時區資料

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

* `Promise<Response>`. `integrity`在標頭中傳遞參數，以保留預設的完整性檢查行為。

  下列範例 (`wwwroot/index.html`) 將自訂 HTTP 標頭新增至輸出要求，並將 `integrity` 參數傳遞至 `fetch` 呼叫：
  
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

* `null`/`undefined`，這會導致預設的載入行為。

外部來源必須傳回瀏覽器所需的 CORS 標頭，以允許跨原始來源資源載入。 依預設，Cdn 通常會提供必要的標頭。

您只需要指定自訂行為的類型。 未指定的類型 `loadBootResource` 會根據其預設載入行為載入架構。

## <a name="change-the-filename-extension-of-dll-files"></a>變更 DLL 檔案的副檔名

如果您需要變更應用程式已發佈檔案的副檔名 `.dll` ，請遵循本節中的指導方針。

發佈應用程式之後，請使用 shell 腳本或 DevOps 組建管線，將檔案重新命名 `.dll` 以使用不同的副檔名。 以 `.dll` `wwwroot` 應用程式發佈輸出的目錄中的檔案為目標 (例如 `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 。

在下列範例中， `.dll` 會將檔案重新命名為使用 `.bin` 副檔名。

在 Windows 上：

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

如果服務工作者資產也在使用中，請新增下列命令：

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

在 Linux 或 macOS 上：

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

如果服務工作者資產也在使用中，請新增下列命令：

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
若要使用不同的副檔名 `.bin` ，請 `.bin` 在上述命令中取代。

若要處理壓縮 `blazor.boot.json.gz` 和檔案 `blazor.boot.json.br` ，請採用下列其中一種方法：

* 移除壓縮的 `blazor.boot.json.gz` 和檔案 `blazor.boot.json.br` 。 這種方法會停用壓縮。
* 重新壓縮更新後的檔案 `blazor.boot.json` 。

先前的指導方針也適用于服務工作者資產正在使用中。 移除或重新壓縮 `wwwroot/service-worker-assets.js.br` 和 `wwwroot/service-worker-assets.js.gz` 。 否則，瀏覽器中的檔案完整性檢查會失敗。

下列 Windows 範例使用放置於專案根目錄的 PowerShell 腳本。

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

如果服務工作者資產也在使用中，請新增下列命令：

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

在專案檔中，腳本會在發佈應用程式之後執行：

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> 重新命名和消極式載入相同的元件時，請參閱中的指導方針 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> 。

## <a name="resolve-integrity-check-failures"></a>解決完整性檢查失敗

當 Blazor WebAssembly 下載應用程式的啟動檔案時，它會指示瀏覽器對回應執行完整性檢查。 它會使用檔案中的資訊 `blazor.boot.json` `.dll` ，為、和其他檔案指定預期的 256 sha-1 雜湊值 `.wasm` 。 這項功能很有用，原因如下：

* 它可確保您不會在載入不一致的檔案集時產生風險，例如，當使用者在下載應用程式檔的過程中，將新的部署套用至您的 web 伺服器。 不一致的檔案可能會導致未定義的行為。
* 它可確保使用者的瀏覽器永遠不會快取不一致或不正確回應，這可能會讓他們無法啟動應用程式，即使它們手動重新整理頁面也是如此。
* 這可讓您安全地快取回應，甚至不檢查伺服器端的變更，直到預期的 256 SHA-1 雜湊本身變更為止，因此後續頁面載入牽涉到較少的要求，並更快完成。

如果您的 web 伺服器傳回的回應不符合預期的 256 SHA-1 雜湊，您將會在瀏覽器的開發人員主控台中看到類似下面的錯誤：

> 在 https://myapp.example.com/\_framework/My Blazor 計算 SHA-256 完整性 ' IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY = ' 的資源 'App.dll ' 的 ' 完整性 ' 屬性中找不到有效的摘要。 資源已被封鎖。

在大部分的情況下，這 *不* 是完整性檢查本身的問題。 相反地，這表示有其他問題，而完整性檢查則會警告您有關該其他問題。

### <a name="diagnosing-integrity-problems"></a>診斷完整性問題

建立應用程式時，產生的 `blazor.boot.json` 資訊清單會描述您開機資源的 256 sha-1 雜湊 (例如，、 `.dll` 以及 `.wasm` 產生組建輸出時) 的其他檔案。 只要 SHA-256 雜湊 `blazor.boot.json` 符合傳遞給瀏覽器的檔案，完整性檢查就會通過。

這種失敗的常見原因如下：

 * Web 服務器的回應是錯誤 (例如， *找不到 404* 或 *500-Internal server 錯誤*) ，而不是瀏覽器要求的檔案。 瀏覽器會將此報告為完整性檢查失敗，而不是回應失敗。
 * 在檔案的組建和傳遞之間，檔案的內容已變更至瀏覽器。 這可能會發生：
   * 如果您或組建工具手動修改組建輸出。
   * 如果部署程式的某些層面修改了檔案。 例如，如果您使用以 Git 為基礎的部署機制，請記住，如果您在 Windows 上認可檔案並在 Linux 上查看檔案，則 Git 會以透明的方式將 Windows 樣式的行尾結束符號轉換為 Unix 樣式的行尾結束符號。 變更檔案行尾結束符號會變更 256 SHA-1 雜湊。 若要避免這個問題，請考慮 [使用 `.gitattributes` 將組建構件 `binary` 視為](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)檔案。
   * Web 服務器會在提供檔案內容的過程中修改檔案內容。 例如，某些內容散發網路 (Cdn) 會自動嘗試 [縮短](xref:client-side/bundling-and-minification#minification) HTML，進而修改它。 您可能需要停用這類功能。

若要診斷下列哪一個適用于您的案例：

 1. 注意哪個檔案會藉由讀取錯誤訊息來觸發錯誤。
 1. 開啟瀏覽器的開發人員工具，並查看 [ *網路* ] 索引標籤。如有必要，請重載頁面以查看要求和回應的清單。 尋找在該清單中觸發錯誤的檔案。
 1. 檢查回應中的 HTTP 狀態碼。 如果伺服器傳回 *200-OK* (或另一個2xx 狀態碼) 以外的任何一個，則表示您有伺服器端的問題要進行診斷。 例如，狀態碼403表示有授權問題，而狀態碼500表示伺服器以未指定的方式失敗。 請參閱伺服器端記錄來診斷並修正應用程式。
 1. 如果資源的狀態碼是 *200-確定* ，請查看瀏覽器開發人員工具中的回應內容，並檢查內容是否符合預期的資料。 例如，常見的問題是 jeffv 路由，讓要求即使是其他檔案也會傳回您的 `index.html` 資料。 請確定要求的回應 `.wasm` 是 WebAssembly 二進位檔，而要求的回應 `.dll` 是 .net 元件二進位檔。 如果沒有，您就有伺服器端路由問題需要進行診斷。
 1. 搜尋以使用 [疑難排解完整性 PowerShell 腳本](#troubleshoot-integrity-powershell-script)來驗證應用程式的已發佈和已部署輸出。

如果您確認伺服器傳回義正辭嚴正確的資料，就必須在檔案的組建和傳遞之間修改內容。 若要調查這一點：

 * 檢查組建工具鏈和部署機制，以防在建立檔案之後修改檔案。 例如，如先前所述，Git 會轉換檔案行尾結束符號。
 * 檢查 web 伺服器或 CDN 設定，以防它們設定為動態修改回應 (例如，嘗試縮短 HTML) 。 Web 服務器可正常執行 HTTP 壓縮 (例如，傳回 `content-encoding: br` 或 `content-encoding: gzip`) ，因為這不會影響解壓縮之後的結果。 不過，網頁伺服器 *不* 能修改未壓縮的資料。

### <a name="troubleshoot-integrity-powershell-script"></a>針對完整性 PowerShell 腳本進行疑難排解

使用 [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell 腳本來驗證已發佈和已部署的 Blazor 應用程式。 當應用程式有架構無法識別的完整性問題時，就會提供此腳本作為起點 Blazor 。 您的應用程式可能需要自訂腳本。

腳本會檢查資料夾中的檔案 `publish` ，並從已部署的應用程式下載，以偵測包含完整性雜湊之不同資訊清單中的問題。 這些檢查應該會偵測到最常見的問題：

* 您已在已發行的輸出中修改檔案，但未加以察覺。
* 應用程式未正確部署至部署目標，或部署目標環境內的某個變更。
* 已部署的應用程式和發行應用程式的輸出之間有一些差異。

在 PowerShell 命令 shell 中使用下列命令叫用腳本：

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

占 位 符：

* `{BASE URL}`：已部署應用程式的 URL。
* `{PUBLISH OUTPUT FOLDER}`：應用程式用來 `publish` 部署應用程式的資料夾或位置的路徑。

> [!NOTE]
> 若要將 `dotnet/AspNetCore.Docs` GitHub 存放庫複製到使用 [Bitdefender](https://www.bitdefender.com) 病毒掃描程式的系統，請將例外狀況新增至 `integrity.ps1` 腳本的 Bitdefender。 在複製存放庫之前，將例外狀況新增至 Bitdefender，以避免病毒掃描程式隔離腳本。 下列範例是 Windows 系統上複製之存放庫的腳本一般路徑。 視需要調整路徑。 預留位置 `{USER}` 是使用者的路徑區段。
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a>停用非 PWA 應用程式的完整性檢查

在大多數情況下，請勿停用完整性檢查。 停用完整性檢查並無法解決造成非預期回應的根本問題，因而導致遺失先前所列的權益。

在某些情況下，無法依賴網頁伺服器傳回一致的回應，而且您沒有任何選擇，而是停用完整性檢查。 若要停用完整性檢查，請將下列內容新增至專案檔中的屬性群組 Blazor WebAssembly `.csproj` ：

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

`BlazorCacheBootResources` 也會 Blazor `.dll` 根據其 256 sha-1 雜湊停用快取、和其他檔案的預設行為， `.wasm` 因為屬性指出無法依賴 sha-256 雜湊來取得正確性。 即使使用此設定，瀏覽器的一般 HTTP 快取仍可能會快取這些檔案，但這是否發生，取決於您的 web 伺服器設定和它所提供的 `cache-control` 標頭。

> [!NOTE]
> `BlazorCacheBootResources`屬性不會停用[漸進式 Web 應用程式的完整性檢查 (pwa) ](xref:blazor/progressive-web-app)。 如需 Pwa 的相關指引，請參閱 [停用 pwa 的完整性檢查](#disable-integrity-checking-for-pwas) 一節。

### <a name="disable-integrity-checking-for-pwas"></a>停用 Pwa 的完整性檢查

Blazor的漸進式 Web 應用程式 (PWA) 範本包含建議的檔案 `service-worker.published.js` ，該檔案負責提取和儲存應用程式檔，以供離線使用。 這是與一般應用程式啟動機制不同的程式，且具有自己的個別完整性檢查邏輯。

在檔案中 `service-worker.published.js` ，下列程式程式碼存在：

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

若要停用完整性檢查，請將這 `integrity` 一行變更為下列程式碼來移除參數：

```javascript
.map(asset => new Request(asset.url));
```

同樣地，停用完整性檢查即表示您遺失完整性檢查所提供的安全性保證。 例如，如果使用者的瀏覽器在您部署新版本的確切時刻快取了應用程式，則可能會有一個風險，它可以從舊的部署快取一些檔案，而有些則會從新的部署中快取。 如果發生這種情況，應用程式就會變成處於中斷狀態，直到您部署進一步的更新。
