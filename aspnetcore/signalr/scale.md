---
title: ASP.NET Core SignalR 生產裝載和調整規模
author: bradygaster
description: 瞭解如何在使用 ASP.NET Core 的應用程式中避免效能和調整問題 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564010"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core SignalR 裝載和調整

[Andrew Stanton-護士](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)

本文說明使用 ASP.NET Core 之高流量應用程式的裝載和調整考慮 SignalR 。

## <a name="sticky-sessions"></a>粘滯話

SignalR 要求特定連接的所有 HTTP 要求都必須由相同的伺服器進程處理。 當 SignalR (多部) 伺服器的伺服器陣列上執行時，必須使用「粘滯話」。 某些負載平衡器也會將「粘滯話」稱為會話親和性。 Azure App Service 使用 [應用程式要求路由](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) 來路由傳送要求。 啟用 Azure App Service 中的 [ARR 親和性] 設定，將會啟用「粘滯話」。 不需要執行任何粘滯會話的唯一情況是：

1. 在單一伺服器上裝載時，在單一進程中。
1. 使用 Azure 服務時 SignalR 。
1. 當所有用戶端都設定為 **僅** 使用 websocket， **且** 用戶端設定中已啟用 [SkipNegotiation 設定](xref:signalr/configuration#configure-additional-options) 時。

在所有其他情況下 (包括何時使用 Redis 後擋板) ，則必須針對粘滯話設定伺服器環境。

如需設定 Azure App Service 的指引 SignalR ，請參閱 <xref:signalr/publish-to-azure-web-app> 。

## <a name="tcp-connection-resources"></a>TCP 連接資源

Web 服務器可支援的並行 TCP 連線數目有限。 標準 HTTP 用戶端會使用 *暫時* 連接。 當用戶端閒置並在稍後重新開啟時，可以關閉這些連接。 另一方面， SignalR 連接是 *永久性* 的。 SignalR 即使用戶端閒置，連線仍會保持開啟。 在提供許多用戶端的高流量應用程式中，這些持續連線可能會導致伺服器達到其最大連線數目。

持續性連接也會耗用一些額外的記憶體，以追蹤每個連接。

大量使用連接相關的資源， SignalR 可能會影響裝載于相同伺服器上的其他 web 應用程式。 當 SignalR 開啟並保存最後一個可用的 TCP 連線時，相同伺服器上的其他 web 應用程式也不會有其他可用的連接。

如果伺服器用盡連接，您會看到隨機的通訊端錯誤和連線重設錯誤。 例如：

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

若要讓 SignalR 資源使用狀況在其他 web 應用程式中造成錯誤，請 SignalR 在不同于其他 web 應用程式的伺服器上執行。

若要防止 SignalR 資源使用在應用程式中造成錯誤 SignalR ，請相應放大以限制伺服器必須處理的連接數目。

## <a name="scale-out"></a>擴增

使用的應用程式 SignalR 需要追蹤其所有連接，這會造成伺服器陣列的問題。 新增伺服器，並取得其他伺服器不知道的新連接。 例如，在 SignalR 下圖中的每部伺服器上，並不知道其他伺服器上的連接。 當 SignalR 其中一個伺服器想要將訊息傳送給所有用戶端時，訊息只會傳送到連接到該伺服器的用戶端。

![調整：：：非 loc (SignalR) ：：：沒有背板](scale/_static/scale-no-backplane.png)

解決此問題的選項是 [Azure SignalR 服務](#azure-signalr-service) 和 [Redis 背板](#redis-backplane)。

## <a name="azure-signalr-service"></a>Azure SignalR 服務

Azure SignalR 服務是 proxy，而不是背板。 每次用戶端啟動伺服器的連線時，就會將用戶端重新導向以連接至服務。 下圖說明該流程：

![建立連至 Azure：：：無 loc (SignalR) ：：： Service 的連線](scale/_static/azure-signalr-service-one-connection.png)

結果是，服務會管理所有用戶端連線，而每個伺服器只需要少量的服務連接，如下圖所示：

![連線至服務的用戶端，連線到服務的伺服器](scale/_static/azure-signalr-service-multiple-connections.png)

這種向外延展的方法有幾個優點，而不是 Redis 背板的替代方案：

* 由於用戶端連線時，會立即將用戶端重新導向至 Azure 服務，因此不需要像是 [用戶端親和性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)的「粘滯話」 SignalR 。
* SignalR應用程式可以根據傳送的訊息數目進行擴充，而 Azure 服務則會 SignalR 調整以處理任何數量的連線。 例如，可能有數千個用戶端，但如果每秒只傳送幾個訊息，則 SignalR 應用程式不需要向外延展至多部伺服器，只要處理連線本身即可。
* SignalR應用程式不會使用與 web 應用程式明顯更多的連接資源 SignalR 。

基於這些理由，我們建議 azure SignalR 服務裝載 SignalR 于 azure 上的所有 ASP.NET Core 應用程式，包括 App Service、vm 和容器。

如需詳細資訊，請參閱 [Azure SignalR 服務檔](/azure/azure-signalr/signalr-overview)。

## <a name="redis-backplane"></a>Redis 後擋板

[Redis](https://redis.io/) 是記憶體中的索引鍵/值存放區，可支援具有發佈/訂閱模型的訊息系統。 SignalRRedis 背板使用 pub/sub 功能將訊息轉送至其他伺服器。 當用戶端建立連接時，會將連接資訊傳遞給後端。 當伺服器想要將訊息傳送給所有用戶端時，它會傳送至後端。 後擋板知道所有連線的用戶端，以及它們所在的伺服器。 它會透過各自的伺服器將訊息傳送給所有用戶端。 下圖說明此程式：

![Redis 後擋板，從一部伺服器傳送到所有用戶端的訊息](scale/_static/redis-backplane.png)

針對裝載在您自己的基礎結構上的應用程式，Redis 背板是建議的向外延展方法。 如果您的資料中心與 Azure 資料中心之間有明顯的連線延遲，則 SignalR 對於低延遲或高輸送量需求的內部部署應用程式，Azure 服務可能不是實際的選項。

稍早所述的 Azure SignalR 服務優點是 Redis 背板的缺點：

* 除非下列 **兩項** 都成立，否則需要有粘滯話（也稱為 [用戶端親和性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)）：
  * 所有用戶端都設定為 **僅** 使用 websocket。
  * 用戶端設定中已啟用 [SkipNegotiation 設定](xref:signalr/configuration#configure-additional-options) 。 
   一旦在伺服器上起始連接，連接就必須保持在該伺服器上。
* SignalR應用程式必須根據用戶端的數目進行相應放大，即使傳送的訊息數很少。
* SignalR應用程式在沒有的情況下，會使用比 web 應用程式更多的連接資源 SignalR 。

## <a name="iis-limitations-on-windows-client-os"></a>Windows 用戶端作業系統上的 IIS 限制

Windows 10 和 Windows 8. x 是用戶端作業系統。 用戶端作業系統上的 IIS 有10個並行連接的限制。 SignalR的連接包括：

* 暫時性且經常重新建立。
* 不再使用時，**不會** 立即處置。

上述條件讓它有可能達到用戶端作業系統上的10個連接限制。 當用戶端作業系統用於開發時，建議您：

* 避免 IIS。
* 使用 Kestrel 或 IIS Express 作為部署目標。

## <a name="linux-with-nginx"></a>使用 Nginx 的 Linux

以下包含啟用 Websocket、ServerSentEvents 和 LongPolling 的最低必要設定 SignalR ：

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

使用多個後端伺服器時，必須新增「粘滯話」以防止連線 SignalR 在連接時切換伺服器。 有多種方式可以在 Nginx 中新增多個會話。 下列兩種方法會根據您可用的內容顯示在下方。

除了先前的設定之外，還會新增下列各項。 在下列範例中， `backend` 是伺服器群組的名稱。

使用 [Nginx 開放原始](https://nginx.org/en/)碼時， `ip_hash` 可根據用戶端的 IP 位址，使用將連接路由至伺服器：

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

使用 [Nginx Plus](https://www.nginx.com/products/nginx)，請使用將 `sticky` 新增 cookie 至要求，並將使用者的要求釘選到伺服器：

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

最後，將 `proxy_pass http://localhost:5000` 區段中的變更 `server` 為 `proxy_pass http://backend` 。

如需有關 Websocket over Nginx 的詳細資訊，請參閱 [Nginx 作為 Websocket Proxy](https://www.nginx.com/blog/websocket-nginx)。

如需負載平衡和粘滯話的詳細資訊，請參閱 [NGINX 負載平衡](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)。

如需有關 Nginx ASP.NET Core 的詳細資訊，請參閱下列文章：
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a>協力廠商 SignalR 背板提供者

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [新奧爾良](https://github.com/OrleansContrib/SignalR.Orleans)
* [Rebus](https://github.com/rebus-org/Rebus.SignalR)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure SignalR 服務檔](/azure/azure-signalr/signalr-overview)
* [設定 Redis 背板](xref:signalr/redis-backplane)
