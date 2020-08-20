---
title: 使用 JavaScript 服務在 ASP.NET Core 中建立單一頁面應用程式
author: scottaddie
description: 瞭解使用 JavaScript 服務建立單一頁面應用程式 (SPA) ASP.NET Core 支援的優點。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 379a8f52dab36d331bc42c1fee8d64b3971e9e91
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625657"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>使用 JavaScript 服務在 ASP.NET Core 中建立單一頁面應用程式

由 [Scott Addie](https://github.com/scottaddie) 和 [Fiyaz Hasan](https://fiyazhasan.me/)

單一頁面應用程式 (SPA) 是一種熱門的 web 應用程式，因為其固有的豐富使用者體驗。 將用戶端 SPA 架構或程式庫（例如 [角度](https://angular.io/) 或 [回應](https://facebook.github.io/react/)）與伺服器端架構（例如 ASP.NET Core）相整合可能很困難。 JavaScript 服務的開發目的是要減少整合過程中的麻煩。 它能夠在不同的用戶端和伺服器技術堆疊之間進行順暢的操作。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 本文所述的功能已于 ASP.NET Core 3.0 淘汰。 更簡單的 SPA 架構整合機制可在 [AspNetCore. SpaServices （延伸](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) ） NuGet 套件中取得。 如需詳細資訊，請參閱 [[公告] Obsoleting AspNetCore. SpaServices 和 AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890)。

::: moniker-end

## <a name="what-is-javascript-services"></a>什麼是 JavaScript 服務

JavaScript 服務是 ASP.NET Core 用戶端技術的集合。 其目標是要將 ASP.NET Core 定位為開發人員慣用的伺服器端平臺以建立 Spa。

JavaScript 服務是由兩個不同的 NuGet 套件所組成：

* [AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices) 
* [AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices) 

這些套件在下列案例中很有用：

* 在伺服器上執行 JavaScript
* 使用 SPA 架構或程式庫
* 使用 Webpack 建立用戶端資產

本文中的大部分焦點都放在使用 SpaServices 套件。

## <a name="what-is-spaservices"></a>什麼是 SpaServices

建立 SpaServices 是為了將 ASP.NET Core 定位為開發人員慣用的伺服器端平臺，以建立 Spa。 SpaServices 不需要使用 ASP.NET Core 開發 Spa，也不會鎖定開發人員進入特定的用戶端架構。

SpaServices 提供實用的基礎結構，例如：

* [伺服器端預先呈現](#server-side-prerendering)
* [Webpack Dev 中介軟體](#webpack-dev-middleware)
* [熱模組更換](#hot-module-replacement)
* [路由協助程式](#routing-helpers)

這些基礎結構元件共同強化了開發工作流程和執行時間體驗。 元件可以個別採用。

## <a name="prerequisites-for-using-spaservices"></a>使用 SpaServices 的必要條件

若要使用 SpaServices，請安裝下列各項：

* 使用 npm (6 版或更新版本) 的[Node.js](https://nodejs.org/)

  * 若要確認已安裝且可找到這些元件，請從命令列執行下列命令：

    ```console
    node -v && npm -v
    ```

  * 如果部署至 Azure 網站，則不需要採取任何動作， &mdash;Node.js 已安裝且可在伺服器環境中使用。

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * 在使用 Visual Studio 2017 的 Windows 上，您可以藉由選取 **.Net Core 跨平臺開發** 工作負載來安裝 SDK。

* [AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 套件

## <a name="server-side-prerendering"></a>伺服器端預先呈現

通用 (也稱為 isomorphic) 應用程式是一種 JavaScript 應用程式，可以在伺服器和用戶端上執行。 角度、反應和其他熱門架構都提供此應用程式開發樣式的通用平臺。 其概念是先透過 Node.js 在伺服器上轉譯架構元件，再將進一步的執行委派給用戶端。

SpaServices [提供的](xref:mvc/views/tag-helpers/intro) ASP.NET Core 標籤協助程式可透過叫用伺服器上的 JavaScript 函式，簡化伺服器端預先呈現的執行。

### <a name="server-side-prerendering-prerequisites"></a>伺服器端預先呈現必要條件

安裝 [aspnet 預先呈現](https://www.npmjs.com/package/aspnet-prerendering) 的 npm 套件：

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>伺服器端預先呈現設定

標記協助程式可透過專案的 *_ViewImports cshtml* 檔案中的命名空間註冊來進行探索：

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

這些標籤協助程式會在視圖內利用類似 HTML 的語法，以簡化與低層級 Api 直接通訊的複雜性 Razor ：

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-的模組標籤協助程式

上述程式碼範例中使用的標籤協助程式會透過 `asp-prerender-module` Node.js，在伺服器上執行 *ClientApp/dist/main-server.js* 。 為了清楚起見， *main-server.js* 檔案是 [Webpack](https://webpack.github.io/) 組建程式中 TypeScript 到 JavaScript 轉譯工作的成品。 Webpack 會定義的進入點別名 `main-server` ; 而且，此別名的相依性圖形的遍歷會從 *ClientApp/boot.ini* 檔案開始：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

在下列角度範例中， *ClientApp/boot.ini* 檔案會利用 npm 封裝的函式 `createServerRenderer` 和類型，透過 `RenderResult` `aspnet-prerendering` Node.js 設定伺服器轉譯。 以伺服器端轉譯為目標的 HTML 標籤會傳遞至解析函式呼叫，此呼叫會包裝在強型別 JavaScript `Promise` 物件中。 `Promise`物件的重要性是，它會以非同步方式將 HTML 標籤提供給頁面，以便在 DOM 的預留位置專案中插入。

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-預先呈現-資料標記協助程式

加上標籤協助程式時，標籤協助 `asp-prerender-module` `asp-prerender-data` 程式可以用來將內容資訊從 Razor 視圖傳遞到伺服器端 JavaScript。 例如，下列標記會將使用者資料傳遞給 `main-server` 模組：

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

接收的 `UserName` 引數會使用內建的 JSON 序列化程式進行序列化，並儲存在 `params.data` 物件中。 在下列角度範例中，資料會用來在元素內建立個人化問候語 `h1` ：

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

以標記協助程式傳遞的屬性名稱會以 **PascalCase** 標記法來表示。 相較于 JavaScript，相同的屬性名稱會以 **camelCase**表示。 預設的 JSON 序列化設定負責這項差異。

若要展開上述的程式碼範例，您可以藉由 hydrating 提供給函數的屬性，將資料從伺服器傳遞到視圖 `globals` `resolve` ：

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

在 `postList` 物件內定義的陣列 `globals` 會附加至瀏覽器的全域 `window` 物件。 此變數 hoisting 至全域範圍會排除重複的工作，特別是在伺服器上，以及在用戶端上再次載入相同的資料。

![附加至 window 物件的全域 postList 變數](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack Dev 中介軟體

[Webpack Dev 中介軟體](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) 引進了簡化的開發工作流程，讓 Webpack 依需求建立資源。 當瀏覽器中重載頁面時，中介軟體會自動編譯並服務用戶端資源。 替代方法是在協力廠商相依性或自訂程式碼變更時，透過專案的 npm 組建腳本來手動叫用 Webpack。 下列範例會顯示 *package.json* 檔案中的 npm 組建腳本：

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Webpack Dev 中介軟體必要條件

安裝 [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm 套件：

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Webpack Dev 中介軟體設定

Webpack Dev 中介軟體會透過 *Startup.cs* 檔的方法中的下列程式碼註冊至 HTTP 要求管線 `Configure` ：

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseWebpackDevMiddleware`必須先呼叫擴充方法，才能透過擴充方法[註冊靜態檔案裝載](xref:fundamentals/static-files) `UseStaticFiles` 。 基於安全性理由，只有在應用程式在開發模式中執行時，才註冊中介軟體。

*webpack.config.js*檔案的 `output.publicPath` 屬性會告知中介軟體監看 `dist` 資料夾的變更：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>熱模組更換

考慮 Webpack 的 [熱模組取代](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) 功能，作為 [Webpack 開發中介軟體](#webpack-dev-middleware)的演進。 HMR 引進了所有相同的優點，但它會在編譯變更後自動更新頁面內容，藉此簡化開發工作流程。 請勿將這項功能與重新整理瀏覽器混淆，這會干擾目前的記憶體內部狀態和 SPA 的偵測會話。 Webpack Dev 中介軟體服務和瀏覽器之間有即時連結，這表示會將變更推送至瀏覽器。

### <a name="hot-module-replacement-prerequisites"></a>熱模組更換必要條件

安裝 [webpack-熱中介軟體](https://www.npmjs.com/package/webpack-hot-middleware) npm 套件：

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>熱模組更換設定

HMR 元件必須在方法中註冊至 MVC 的 HTTP 要求管線 `Configure` ：

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

如同 [Webpack Dev 中介軟體](#webpack-dev-middleware)的情況一樣，您 `UseWebpackDevMiddleware` 必須在擴充方法之前呼叫擴充方法 `UseStaticFiles` 。 基於安全性理由，只有在應用程式在開發模式中執行時，才註冊中介軟體。

*webpack.config.js*檔案必須定義 `plugins` 陣列，即使它是空的也一樣：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

在瀏覽器中載入應用程式之後，[開發人員工具] 的 [主控台] 索引標籤會提供 HMR 啟用的確認：

![熱模組取代連接的訊息](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>路由協助程式

在大部分以 ASP.NET Core 為基礎的 Spa 中，除了伺服器端路由，通常還需要用戶端路由。 SPA 和 MVC 路由系統可以獨立運作，而不會受到干擾。 不過，有一個邊緣案例面臨挑戰：識別 404 HTTP 回應。

請考慮使用無副檔名路由的案例 `/some/page` 。 假設要求不會與伺服器端路由進行模式比對，但其模式與用戶端路由相符。 現在請考慮的連入要求 `/images/user-512.png` ，這通常預期會在伺服器上找到影像檔案。 如果要求的資源路徑不符合任何伺服器端路由或靜態檔案，用戶端應用程式不太可能會處理此問題， &mdash; 通常會傳回所需的 404 HTTP 狀態碼。

### <a name="routing-helpers-prerequisites"></a>路由協助程式必要條件

安裝用戶端路由 npm 套件。 使用角度作為範例：

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>路由協助程式設定

`MapSpaFallbackRoute`方法中會使用名為的擴充方法 `Configure` ：

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

路由會依照其設定順序進行評估。 因此， `default` 會先使用上述程式碼範例中的路由進行模式比對。

## <a name="create-a-new-project"></a>建立新專案

JavaScript 服務提供預先設定的應用程式範本。 SpaServices 可用於這些範本，並搭配不同的架構和程式庫，例如角度、反應和 Redux。

您可以藉由執行下列命令，透過 .NET Core CLI 安裝這些範本：

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

會顯示可用的 SPA 範本清單：

| 範本                                 | 簡短名稱 | Language | 標籤        |
| ------------------------------------------| :--------: | :------: | :---------: |
| 具有角度的 MVC ASP.NET Core             | angular    | [C#]     | Web/MVC/SPA |
| 使用 React.js 的 MVC ASP.NET Core            | react      | [C#]     | Web/MVC/SPA |
| 使用 React.js 和 Redux 的 MVC ASP.NET Core  | reactredux | [C#]     | Web/MVC/SPA |

若要使用其中一個 SPA 範本建立新的專案，請在[dotnet new](/dotnet/core/tools/dotnet-new)命令中包含範本的**簡短名稱**。 下列命令會建立一個具有針對伺服器端設定 ASP.NET Core MVC 的「角」應用程式：

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>設定執行時間設定模式

有兩個主要執行時間設定模式：

* **開發**：
  * 包含來源對應以簡化調試。
  * 不會優化用戶端程式代碼的效能。
* **生產環境**：
  * 排除來源對應。
  * 透過組合和縮制優化用戶端程式代碼。

ASP.NET Core 會使用名為的環境變數 `ASPNETCORE_ENVIRONMENT` 來儲存設定模式。 如需詳細資訊，請參閱 [設定環境](xref:fundamentals/environments#set-the-environment)。

### <a name="run-with-net-core-cli"></a>使用 .NET Core CLI 執行

在專案根目錄中執行下列命令，以還原必要的 NuGet 和 npm 套件：

```dotnetcli
dotnet restore && npm i
```

建立並執行應用程式：

```dotnetcli
dotnet run
```

應用程式會根據執行時間設定 [模式](#set-the-runtime-configuration-mode)在 localhost 上啟動。 `http://localhost:5000`在瀏覽器中流覽至會顯示登陸頁面。

### <a name="run-with-visual-studio-2017"></a>使用 Visual Studio 2017 執行

開啟[dotnet new](/dotnet/core/tools/dotnet-new)命令所產生的 *.csproj*檔案。 當專案開啟時，會自動還原必要的 NuGet 和 npm 套件。 此還原程式最多可能需要幾分鐘的時間，而且應用程式會在完成時準備好執行。 按一下綠色的 [執行] 按鈕或按下 `Ctrl + F5` ，瀏覽器會開啟至應用程式的登陸頁面。 應用程式會根據執行時間設定 [模式](#set-the-runtime-configuration-mode)在 localhost 上執行。

## <a name="test-the-app"></a>測試應用程式

SpaServices 範本已預先設定為使用 [Karma](https://karma-runner.github.io/1.0/index.html) 和 [Jasmine](https://jasmine.github.io/)執行用戶端測試。 Jasmine 是適用于 JavaScript 的熱門單元測試架構，而 Karma 則是適用于這些測試的測試執行器。 Karma 已設定為使用 [Webpack Dev 中介軟體](#webpack-dev-middleware) ，因此開發人員不需要在每次進行變更時停止和執行測試。 無論是針對測試案例或測試案例本身執行的程式碼，測試都會自動執行。

使用「角度」應用程式做為範例中，已為 Jasmine 測試案例提供的範例檔案中有兩個 `CounterComponent` ： *counter.component.spec.ts*

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

在 *ClientApp* 目錄中開啟命令提示字元。 執行以下命令：

```console
npm test
```

腳本會啟動 Karma 測試執行器，以讀取 *karma.conf.js* 檔案中定義的設定。 在其他設定中， *karma.conf.js* 會透過其陣列來識別要執行的測試檔案 `files` ：

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>發佈應用程式

如需發佈至 Azure 的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/12474) 。

將產生的用戶端資產和已發佈的 ASP.NET Core 成品合併為立即部署的套件可能會很麻煩。 幸好，SpaServices 會使用名為的自訂 MSBuild 目標來協調整個發行流程 `RunWebpack` ：

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

MSBuild 目標有下列責任：

1. 還原 npm 封裝。
1. 建立協力廠商用戶端資產的生產層級組建。
1. 建立自訂用戶端資產的生產層級組建。
1. 將 Webpack 產生的資產複製到 [發佈] 資料夾。

執行下列動作時，會叫用 MSBuild 目標：

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>其他資源

* [角度檔](https://angular.io/docs)
