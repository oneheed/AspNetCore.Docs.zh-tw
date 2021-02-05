---
title: 裝載和部署 ASP.NET Core Blazor Server
author: guardrex
description: 瞭解如何使用 ASP.NET Core 來裝載和部署 Blazor Server 應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "94595437"
---
# <a name="host-and-deploy-blazor-server"></a>裝載和部署 Blazor Server

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>主機組態值

[ Blazor Server 應用程式](xref:blazor/hosting-models#blazor-server)可以接受[一般主機配置值](xref:fundamentals/host/generic-host#host-configuration)。

## <a name="deployment"></a>部署

使用[ Blazor Server 裝載模型](xref:blazor/hosting-models#blazor-server)， Blazor 會在伺服器上從 ASP.NET Core 應用程式內執行。 UI 更新、事件處理及 JavaScript 呼叫會透過連接來處理 [SignalR](xref:signalr/introduction) 。

需要能夠裝載 ASP.NET Core 應用程式的網路伺服器。 使用命令) 時，Visual Studio 包含 **Blazor Server 應用程式** 專案範本 (`blazorserverside` 範本 [`dotnet new`](/dotnet/core/tools/dotnet-new) 。

## <a name="scalability"></a>延展性

規劃部署，以充分利用應用程式的可用基礎結構 Blazor Server 。 請參閱下列資源來處理 Blazor Server 應用程式的擴充性：

* [應用程式的基本概念 Blazor Server](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>部署伺服器

當考慮單一伺服器的擴充性 (擴大) 時，應用程式可用的記憶體，可能是應用程式在使用者需求增加時將耗盡的第一項資源。 伺服器上的可用記憶體會影響：

* 伺服器可支援的作用中線路數目。
* 用戶端上的 UI 延遲。

如需建立安全且可擴充的 Blazor 伺服器應用程式的指引，請參閱 <xref:blazor/security/server/threat-mitigation> 。

針對基本的 *Hello World* 樣式應用程式，每個線路會使用大約 250 KB 的記憶體。 電路的大小取決於應用程式的程式碼，以及與每個元件相關聯的狀態維護需求。 建議您在開發期間測量應用程式和基礎結構的資源需求，但下列基準可作為規劃部署目標的起點：如果您希望應用程式支援5000的並行使用者，請考慮將至少 1.3 GB 的伺服器記憶體預算為應用程式 (或 ~ 每位使用者) ~ 273 KB。

### <a name="signalr-configuration"></a>SignalR 配置

Blazor Server 應用程式會使用 ASP.NET Core SignalR 與瀏覽器進行通訊。 [ SignalR 的裝載和調整條件](xref:signalr/publish-to-azure-web-app)適用于 Blazor Server 應用程式。

Blazor 使用 Websocket 作為傳輸的最佳方式 SignalR ，是因為延遲、可靠性和 [安全性](xref:signalr/security)較低。 SignalR當 websocket 無法使用時，或當應用程式明確設定為使用長時間輪詢時，就會使用長時間輪詢。 部署至 Azure App Service 時，請將應用程式設定為在服務的 Azure 入口網站設定中使用 Websocket。 如需設定應用程式以進行 Azure App Service 的詳細資訊，請參閱[ SignalR 發佈指導方針](xref:signalr/publish-to-azure-web-app)。

#### <a name="azure-signalr-service"></a>Azure SignalR 服務

我們建議使用 [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps。 服務可讓您將 Blazor Server 應用程式相應增加為大量的並行 SignalR 連接。 此外， SignalR 服務的全球接觸和高效能資料中心大幅有助於降低因地理位置而造成的延遲。

> [!IMPORTANT]
> 當 [websocket](https://wikipedia.org/wiki/WebSocket) 停用時，Azure App Service 會使用 HTTP 長時間輪詢來模擬即時連接。 HTTP 長時間輪詢的速度明顯低於啟用 Websocket 的執行，而不會使用輪詢來模擬用戶端-伺服器連接。
>
> 建議您針對 Blazor Server 部署至 Azure App Service 的應用程式使用 websocket。 [Azure SignalR 服務](xref:signalr/scale#azure-signalr-service)預設會使用 websocket。 如果應用程式不使用 Azure SignalR 服務，請參閱 <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> 。
>
> 如需詳細資訊，請參閱
>
> * [什麼是 Azure SignalR 服務？](/azure/azure-signalr/signalr-overview)
> * [Azure 服務的效能指南 SignalR](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a>組態

若要設定 Azure 服務的應用程式 SignalR ，應用程式必須支援「 *粘滯話*」，其中用戶端會在進行預導 [時重新導向回相同的伺服器](xref:blazor/hosting-models#connection-to-the-server)。 `ServerStickyMode`選項或設定值設定為 `Required` 。 一般而言，應用程式會使用下列 **_其中一_** 種方法來建立設定：

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuration (使用下列 **_其中一_** 種方法) ：
  
     * 在 `appsettings.json` 中：

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * Azure 入口網站中 **的 app** service  >  **設定應用程式設定** (**名稱**： `Azure__SignalR__StickyServerMode` ，**值**： `Required`) 。 如果您布建 [Azure SignalR 服務](#provision-the-azure-signalr-service)，則會自動為應用程式採用此方法。

### <a name="provision-the-azure-signalr-service"></a>布建 Azure SignalR 服務

若要 SignalR 在 Visual Studio 中布建應用程式的 Azure 服務：

1. 在應用程式的 Visual Studio 中建立 Azure 應用程式發佈設定檔 Blazor Server 。
1. 將 **Azure SignalR 服務** 相依性新增至設定檔。 如果 Azure 訂用帳戶沒有預先存在的 Azure SignalR 服務實例可指派給該應用程式，請選取 [ **建立新的 azure SignalR 服務實例** ] 以布建新的服務實例。
1. 將應用程式發佈至 Azure。

SignalR在 Visual Studio 中布建 Azure 服務會自動 [啟用 *粘滯會話*](#configuration) ，並將 SignalR 連接字串新增至 app Service 的設定。

#### <a name="iis"></a>IIS

使用 IIS 時，請啟用：

* [IIS 上的 websocket](xref:fundamentals/websockets#enabling-websockets-on-iis)。
* [使用應用程式要求路由的「粘滯話](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」。

#### <a name="kubernetes"></a>Kubernetes

使用下列 [Kubernetes 注釋來建立輸入](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)定義：

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>使用 Nginx 的 Linux

SignalR若要讓 websocket 正常運作，請確認 proxy 的 `Upgrade` 和 `Connection` 標頭已設定為下列值，而且 `$connection_upgrade` 對應至其中一個：

* 依預設，升級標頭值。
* `close` 當升級標頭遺失或空白時。

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

如需詳細資訊，請參閱下列文章：

* [NGINX 做為 WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/)
* [WebSocket proxy](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>使用 Apache 的 Linux

若要將 Blazor 應用程式裝載在 Linux 上的 Apache，請設定 `ProxyPass` HTTP 和 websocket 流量。

在下例中︰

* Kestrel 伺服器正在主機電腦上執行。
* 應用程式會接聽埠5000上的流量。

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

啟用下列模組：

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

檢查瀏覽器主控台中的 Websocket 錯誤。 範例錯誤：

* Firefox 無法在 ws://the-domain-name.tld/_blazor?id=XXX 建立與伺服器的連接。
* 錯誤：無法啟動傳輸 ' Websocket '：錯誤：傳輸時發生錯誤。
* 錯誤：無法啟動傳輸 ' LongPolling '： TypeError：此傳輸未定義
* 錯誤：無法使用任何可用的傳輸來連接到伺服器。 Websocket 失敗
* 錯誤：如果連接不是處於「已連線」狀態，則無法傳送資料。

如需詳細資訊，請參閱 [Apache 檔](https://httpd.apache.org/docs/current/mod/mod_proxy.html)。

### <a name="measure-network-latency"></a>測量網路延遲

您可以使用[JS interop](xref:blazor/call-javascript-from-dotnet)來測量網路延遲，如下列範例所示：

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

針對合理的 UI 體驗，建議250毫秒或更少的持續性 UI 延遲。
