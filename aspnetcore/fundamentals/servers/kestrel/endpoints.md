---
title: 設定 ASP.NET Core Kestrel web 伺服器的端點
author: rick-anderson
description: 瞭解如何使用 Kestrel （適用于 ASP.NET Core 的跨平臺 web 伺服器）來設定端點。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: d1b682b12e2cdcaf2a77b17f726ac569c46cf095
ms.sourcegitcommit: f67ba959d3cbfe33b32fa6a5eae1a5ae9de18167
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2021
ms.locfileid: "106179726"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>設定 ASP.NET Core Kestrel web 伺服器的端點

ASP.NET Core 預設會繫結至：

* `http://localhost:5000`
* `https://localhost:5001` (當有本機開發憑證存在時)

使用以下各項指定 URL：

* `ASPNETCORE_URLS` 環境變數。
* `--urls` 命令列引數。
* `urls` 主機組態索引鍵。
* `UseUrls` 擴充方法。

使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。 將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。

如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。

開發憑證會建立於：

* 當 [.NET SDK](/dotnet/core/sdk) 安裝完成時。
* [dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。

某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。

專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。

請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 方法，來為 Kestrel 設定 URL 首碼和連接埠。

`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。

`KestrelServerOptions` 配置：

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (動作 \<ListenOptions>) 

指定組態 `Action` 以針對每個指定端點執行。 呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> 在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> ，將不會套用預設值。

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。 組態的範圍必須限於 Kestrel 的組態區段。

`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) 

指定組態 `Action` 以針對每個 HTTPS 端點執行。 呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> 在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> ，將不會套用預設值。

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

設定 Kestrel 使用 HTTPS。

`ListenOptions.UseHttps` 延伸模組：

* `UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。 如果未設定預設憑證，會擲回例外狀況。
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps` 參數：

* `filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。
* `password` 是存取 X.509 憑證資料所需的密碼。
* `configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。 傳回 `ListenOptions`。
* `storeName` 是要從中載入憑證的憑證存放區。
* `subject` 是憑證的主體名稱。
* `allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。
* `location` 是要從中載入憑證的存放區位置。
* `serverCertificate` 是 X.509 憑證。

在生產環境中，必須明確設定 HTTPS。 至少必須提供預設憑證。

支援的組態描述如下：

* 無組態
* 從組態取代預設憑證
* 變更程式碼中的預設值

### <a name="no-configuration"></a>無組態

Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>從組態取代預設憑證

Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。 設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。

在下列 *appsettings.json* 範例中：

* 將設定 `AllowInvalid` 為 `true` 可允許使用不正確憑證 (例如，自我簽署的憑證) 。
* 在下列範例中，未指定憑證 (的任何 HTTPS 端點， `HttpsDefaultCert`) 回復至所定義的憑證 `Certificates:Default` 或開發憑證。

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

結構描述附註：

* 端點名稱不區分 [大小寫](xref:fundamentals/configuration/index#configuration-keys-and-values)。 例如， `HTTPS` and `Https` 是相等的。
* `Url` 參數對每個端點而言都是必要的。 此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。
* 這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。 透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。
* `Certificate` 區段是選擇性的。 如果 `Certificate` 未指定區段，則會使用中定義的預設值 `Certificates:Default` 。 如果沒有可用的預設值，則會使用開發憑證。 如果沒有預設值，而且沒有開發憑證存在，伺服器就會擲回例外狀況，而且無法啟動。
* `Certificate`區段支援多個[憑證來源](#certificate-sources)。
* 您可以在設定中定義任意數目的 [端點，只要](xref:fundamentals/configuration/index) 它們不會造成埠衝突。

#### <a name="certificate-sources"></a>憑證來源

您可以將憑證節點設定為從數個來源載入憑證：

* `Path` 和 `Password` 載入 *.pfx* 檔案。
* `Path``KeyPath`和 `Password` 載入 *pem* / 和 *..*
* `Subject` 以及 `Store` 從憑證存放區載入。

例如，您 `Certificates:Default` 可以將憑證指定為：

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 。

* 您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。
* 可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。 只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。 中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。
* `KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。 這些多載不使用名稱，並且只使用來自組態的預設組態。

### <a name="change-the-defaults-in-code"></a>變更程式碼中的預設值

`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>使用伺服器名稱指示設定端點

[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。 SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。 用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。

您可以透過兩種方式來設定 SNI：

* 在程式碼中建立端點，並使用回呼的主機名稱來選取憑證 <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ServerCertificateSelector%2A> 。
* 在 [設定中設定](xref:fundamentals/configuration/index)主機名稱和 HTTPS 選項之間的對應。 例如，檔案中的 JSON `appsettings.json` 。

### <a name="sni-with-servercertificateselector"></a>SNI 與 `ServerCertificateSelector`

Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。 回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。 下列回呼程式碼可以在 `ConfigureWebHostDefaults` 專案的 *程式 .cs* 檔案的方法呼叫中使用：

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="sni-in-configuration"></a>設定中的 SNI

Kestrel 支援在 configuration 中定義的 SNI。 您可以使用 `Sni` 包含主機名稱和 HTTPS 選項之間對應的物件來設定端點。 連接主機名稱符合選項，且用於該連接。

下列設定會新增名為 `MySniEndpoint` 的端點，該端點會使用 SNI 根據主機名稱選取 HTTPS 選項：

```json
{
  "Kestrel": {
    "Endpoints": {
      "MySniEndpoint": {
        "Url": "https://*",
        "SslProtocols": ["Tls11", "Tls12"],
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": ["Tls11", "Tls12", "Tls13"],
            "Certificate": {
              "Subject": "<subject; required>",
              "Store": "<certificate store; required>",
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "<path to .pfx file>",
              "Password": "<certificate password>"
            }
          },
          "*": {
            // At least one subproperty needs to exist per SNI section or it
            // cannot be discovered via IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

可由 SNI 覆寫的 HTTPS 選項：

* `Certificate` 設定 [憑證來源](#certificate-sources)。
* `Protocols` 設定允許的 [HTTP 通訊協定](xref:Microsoft.AspNetCore.Server.Kestrel.Core.HttpProtocols)。
* `SslProtocols` 設定允許的 [SSL 通訊協定](xref:System.Security.Authentication.SslProtocols)。
* `ClientCertificateMode` 設定 [用戶端憑證需求](xref:Microsoft.AspNetCore.Server.Kestrel.Https.ClientCertificateMode)。

主機名稱支援萬用字元對應：

* 完全相符。 例如，會 `a.example.org` 符合 `a.example.org` 。
* 萬用字元前置詞。 如果有多個萬用字元相符專案，則會選擇最長的模式。 例如， `*.example.org` 符合 `b.example.org` 和 `c.example.org` 。
* 完整萬用字元。 `*` 符合所有其他專案，包括未使用 SNI 但未傳送主機名稱的用戶端。

相符的 SNI 設定會套用至連接的端點，並覆寫端點上的值。 如果連接不符合設定的 SNI 主機名稱，則會拒絕連接。

### <a name="sni-requirements"></a>SNI 需求

* 在目標 framework `netcoreapp2.1` 或更新版本上執行。 在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。 如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。
* 所有網站都在相同的 Kestrel 執行個體上執行。 在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。

## <a name="ssltls-protocols"></a>SSL/TLS 通訊協定

SSL 通訊協定是用來加密和解密兩個對等（傳統是用戶端和伺服器）之間流量的通訊協定。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

預設值為 `SslProtocols.None` ，會讓 Kestrel 使用作業系統預設值選擇最適合的通訊協定。 除非您有特定的原因要選取通訊協定，否則請使用預設值。

## <a name="connection-logging"></a>連接記錄

呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> 以發出連接上位元組層級通訊的 Debug 層級記錄。 連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。 如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。 如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。 這是內建的 [連接中介軟體](#connection-middleware)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>繫結至 TCP 通訊端

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。 若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>繫結至 Unix 通訊端

請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* 在 Nginx 設定檔中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。 `{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> ，例如 `kestrel-test.sock` 上述範例) 。
* 確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。

## <a name="port-0"></a>連接埠 0

指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。 下列範例示範如何判斷在執行時間系結的埠 Kestrel：

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>限制

使用下列方法來設定端點：

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* `--urls` 命令列引數
* `urls` 主機組態索引鍵
* `ASPNETCORE_URLS` 環境變數

要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。 不過，請注意下列限制：

* 除非在 HTTPS 端點設定中提供預設憑證，否則 HTTPS 無法與這些方法搭配使用 (例如，使用設定 `KestrelServerOptions` 或設定檔，如本文稍早所述) 。
* 當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。

## <a name="iis-endpoint-configuration"></a>IIS 端點設定

使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。 如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

`Protocols` 屬性會建立在連線端點上或針對伺服器啟用的 HTTP 通訊協定 (`HttpProtocols`)。 從 `HttpProtocols` 列舉中指派一個值給 `Protocols` 屬性。

| `HttpProtocols` 列舉值 | 允許的連線通訊協定 |
| -------------------------- | ----------------------------- |
| `Http1`                    | 僅限 HTTP/1.1。 可在具有或沒有 TLS 的情況下使用。 |
| `Http2`                    | 僅限 HTTP/2。 只有在用戶端支援[先備知識模式](https://tools.ietf.org/html/rfc7540#section-3.4)時，才可以在沒有 TLS 的情況下使用。 |
| `Http1AndHttp2`            | HTTP/1.1 和 HTTP/2。 HTTP/2 要求用戶端在 TLS [應用層通訊協定協商 ](https://tools.ietf.org/html/rfc7301#section-3) 中選取 HTTP/2， (ALPN) 交握;否則，連接預設為 HTTP/1.1。 |

`ListenOptions.Protocols`任何端點的預設值為 `HttpProtocols.Http1AndHttp2` 。

HTTP/2 的 TLS 限制：

* TLS 1.2 版或更新版本
* 已停用重新交涉
* 已停用壓縮
* 暫時金鑰交換大小下限：
  * 橢圓曲線 Diffie-Hellman (>ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; ：最少224位
  * 有限的欄位 Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; ：最低2048位
* 不禁止加密套件。 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack;預設支援使用 P-256 橢圓曲線 &lbrack; `FIPS186` &rbrack; 。

下列範例會允許連接埠 8000 上的 HTTP/1.1 和 HTTP/2 連線。 這些連線使用提供的憑證受到 TLS 保護：

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

在 Linux 上， <xref:System.Net.Security.CipherSuitesPolicy> 可用來篩選每個連接的 TLS 交握：

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>連接中介軟體

如有需要，自訂連線中介軟體可以針對特定加密，篩選每個連線的 TLS 交握。

下列範例 <xref:System.NotSupportedException> 會針對應用程式不支援的任何加密演算法擲回。 或者，將 [ITlsHandshakeFeature](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 定義和比較成可接受的加密套件清單。

CipherAlgorithmType 不會使用加密 [。 Null](xref:System.Security.Authentication.CipherAlgorithmType) 加密演算法。

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

您也可以透過 lambda 設定連接篩選 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> ：

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>從 configuration 設定 HTTP 通訊協定

`CreateDefaultBuilder` 預設會呼叫 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。

下列 *appsettings.json* 範例會建立 HTTP/1.1 作為所有端點的預設連接通訊協定：

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

下列 *appsettings.json* 範例會建立特定端點的 HTTP/1.1 連接通訊協定：

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

程式碼中指定的通訊協定會覆寫設定所設定的值。

## <a name="url-prefixes"></a>URL 前置詞

使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。

只有 HTTP URL 前置詞有效。 使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。

* IPv4 位址與連接埠號碼

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。

* IPv6 位址與連接埠號碼

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。

* 主機名稱與連接埠號碼

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  主機名稱 `*` 和 `+` 並不特殊。 無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。 若要在相同埠上將不同的主機名稱系結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 proxy 伺服器。 反向 proxy 伺服器範例包括 IIS、Nginx 或 Apache。

  > [!WARNING]
  > 裝載於反向 Proxy 組態需要[主機篩選](xref:fundamentals/servers/kestrel/host-filtering)。

* 主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。 如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。 如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。
