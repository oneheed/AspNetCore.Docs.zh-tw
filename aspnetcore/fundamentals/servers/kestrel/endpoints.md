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
ms.openlocfilehash: f9d82409f4b31a5564c7cdfa48beb303d784e213
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057144"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="96bc2-103">設定 ASP.NET Core Kestrel web 伺服器的端點</span><span class="sxs-lookup"><span data-stu-id="96bc2-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="96bc2-104">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="96bc2-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="96bc2-105">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="96bc2-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="96bc2-106">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="96bc2-106">Specify URLs using the:</span></span>

* <span data-ttu-id="96bc2-107">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="96bc2-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="96bc2-108">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="96bc2-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="96bc2-109">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="96bc2-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="96bc2-110">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="96bc2-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="96bc2-111">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="96bc2-112">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="96bc2-113">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="96bc2-114">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="96bc2-114">A development certificate is created:</span></span>

* <span data-ttu-id="96bc2-115">當 [.NET SDK](/dotnet/core/sdk) 安裝完成時。</span><span class="sxs-lookup"><span data-stu-id="96bc2-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="96bc2-116">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="96bc2-117">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="96bc2-118">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="96bc2-119">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="96bc2-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="96bc2-120">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="96bc2-121">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="96bc2-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="96bc2-122">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="96bc2-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="96bc2-123">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="96bc2-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="96bc2-124">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="96bc2-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="96bc2-125">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="96bc2-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="96bc2-126">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="96bc2-126">Configure(IConfiguration)</span></span>

<span data-ttu-id="96bc2-127">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="96bc2-127">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="96bc2-128">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="96bc2-128">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="96bc2-129">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="96bc2-129">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

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

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="96bc2-130">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="96bc2-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="96bc2-131">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="96bc2-131">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="96bc2-132">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="96bc2-132">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="96bc2-133">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="96bc2-133">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="listenoptionsusehttps"></a><span data-ttu-id="96bc2-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="96bc2-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="96bc2-135">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="96bc2-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="96bc2-136">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="96bc2-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="96bc2-137">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="96bc2-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="96bc2-138">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="96bc2-138">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="96bc2-139">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="96bc2-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="96bc2-140">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="96bc2-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="96bc2-141">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="96bc2-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="96bc2-142">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="96bc2-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="96bc2-143">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="96bc2-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="96bc2-144">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="96bc2-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="96bc2-145">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="96bc2-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="96bc2-146">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="96bc2-147">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="96bc2-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="96bc2-148">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="96bc2-149">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="96bc2-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="96bc2-150">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="96bc2-151">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="96bc2-151">Supported configurations described next:</span></span>

* <span data-ttu-id="96bc2-152">無組態</span><span class="sxs-lookup"><span data-stu-id="96bc2-152">No configuration</span></span>
* <span data-ttu-id="96bc2-153">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="96bc2-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="96bc2-154">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="96bc2-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="96bc2-155">無組態</span><span class="sxs-lookup"><span data-stu-id="96bc2-155">No configuration</span></span>

<span data-ttu-id="96bc2-156">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="96bc2-157">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="96bc2-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="96bc2-158">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="96bc2-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="96bc2-159">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="96bc2-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="96bc2-160">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="96bc2-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="96bc2-161">將設定 `AllowInvalid` 為 `true` 可允許使用不正確憑證 (例如，自我簽署的憑證) 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="96bc2-162">在下列範例中，未指定憑證 (的任何 HTTPS 端點， `HttpsDefaultCert`) 回復至所定義的憑證 `Certificates:Default` 或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates:Default` or the development certificate.</span></span>

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

<span data-ttu-id="96bc2-163">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="96bc2-163">Schema notes:</span></span>

* <span data-ttu-id="96bc2-164">端點名稱不區分 [大小寫](xref:fundamentals/configuration/index#configuration-keys-and-values)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-164">Endpoints names are [case-insensitive](xref:fundamentals/configuration/index#configuration-keys-and-values).</span></span> <span data-ttu-id="96bc2-165">例如， `HTTPS` and `Https` 是相等的。</span><span class="sxs-lookup"><span data-stu-id="96bc2-165">For example, `HTTPS` and `Https` are equivalent.</span></span>
* <span data-ttu-id="96bc2-166">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="96bc2-166">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="96bc2-167">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="96bc2-167">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="96bc2-168">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="96bc2-168">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="96bc2-169">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="96bc2-169">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="96bc2-170">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="96bc2-170">The `Certificate` section is optional.</span></span> <span data-ttu-id="96bc2-171">如果 `Certificate` 未指定區段，則會使用中定義的預設值 `Certificates:Default` 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-171">If the `Certificate` section isn't specified, the defaults defined in `Certificates:Default` are used.</span></span> <span data-ttu-id="96bc2-172">如果沒有可用的預設值，則會使用開發憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-172">If no defaults are available, the development certificate is used.</span></span> <span data-ttu-id="96bc2-173">如果沒有預設值，而且沒有開發憑證存在，伺服器就會擲回例外狀況，而且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="96bc2-173">If there are no defaults and the development certificate isn't present, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="96bc2-174">`Certificate`區段支援多個[憑證來源](#certificate-sources)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-174">The `Certificate` section supports multiple [certificate sources](#certificate-sources).</span></span>
* <span data-ttu-id="96bc2-175">您可以在設定中定義任意數目的 [端點，只要](xref:fundamentals/configuration/index) 它們不會造成埠衝突。</span><span class="sxs-lookup"><span data-stu-id="96bc2-175">Any number of endpoints may be defined in [Configuration](xref:fundamentals/configuration/index) as long as they don't cause port conflicts.</span></span>

#### <a name="certificate-sources"></a><span data-ttu-id="96bc2-176">憑證來源</span><span class="sxs-lookup"><span data-stu-id="96bc2-176">Certificate sources</span></span>

<span data-ttu-id="96bc2-177">您可以將憑證節點設定為從數個來源載入憑證：</span><span class="sxs-lookup"><span data-stu-id="96bc2-177">Certificate nodes can be configured to load certificates from a number of sources:</span></span>

* <span data-ttu-id="96bc2-178">`Path` 和 `Password` 載入 *.pfx* 檔案。</span><span class="sxs-lookup"><span data-stu-id="96bc2-178">`Path` and `Password` to load *.pfx* files.</span></span>
* <span data-ttu-id="96bc2-179">`Path``KeyPath`和 `Password` 載入 *pem* / 和 *..*</span><span class="sxs-lookup"><span data-stu-id="96bc2-179">`Path`, `KeyPath` and `Password` to load *.pem*/*.crt* and *.key* files.</span></span>
* <span data-ttu-id="96bc2-180">`Subject` 以及 `Store` 從憑證存放區載入。</span><span class="sxs-lookup"><span data-stu-id="96bc2-180">`Subject` and `Store` to load from the certificate store.</span></span>

<span data-ttu-id="96bc2-181">例如，您 `Certificates:Default` 可以將憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="96bc2-181">For example, the `Certificates:Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a><span data-ttu-id="96bc2-182">ConfigurationLoader</span><span class="sxs-lookup"><span data-stu-id="96bc2-182">ConfigurationLoader</span></span>

<span data-ttu-id="96bc2-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="96bc2-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="96bc2-184">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-184">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="96bc2-185">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="96bc2-185">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="96bc2-186">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="96bc2-186">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="96bc2-187">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="96bc2-187">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="96bc2-188">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="96bc2-188">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="96bc2-189">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="96bc2-189">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="96bc2-190">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="96bc2-190">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="96bc2-191">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="96bc2-191">Change the defaults in code</span></span>

<span data-ttu-id="96bc2-192">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-192">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="96bc2-193">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="96bc2-193">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="96bc2-194">使用伺服器名稱指示設定端點</span><span class="sxs-lookup"><span data-stu-id="96bc2-194">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="96bc2-195">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="96bc2-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="96bc2-196">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-196">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="96bc2-197">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="96bc2-197">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="96bc2-198">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="96bc2-198">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="96bc2-199">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="96bc2-199">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="96bc2-200">下列回呼程式碼可以在專案的 Program.cs 檔的 `ConfigureWebHostDefaults` 方法呼叫中使用 ：</span><span class="sxs-lookup"><span data-stu-id="96bc2-200">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

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

<span data-ttu-id="96bc2-201">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="96bc2-201">SNI support requires:</span></span>

* <span data-ttu-id="96bc2-202">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="96bc2-202">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="96bc2-203">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-203">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="96bc2-204">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="96bc2-204">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="96bc2-205">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="96bc2-205">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="96bc2-206">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="96bc2-206">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="ssltls-protocols"></a><span data-ttu-id="96bc2-207">SSL/TLS 通訊協定</span><span class="sxs-lookup"><span data-stu-id="96bc2-207">SSL/TLS Protocols</span></span>

<span data-ttu-id="96bc2-208">SSL 通訊協定是用來加密和解密兩個對等（傳統是用戶端和伺服器）之間流量的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="96bc2-208">SSL Protocols are protocols used for encrypting and decrypting traffic between two peers, traditionally a client and a server.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

<span data-ttu-id="96bc2-209">預設值為 `SslProtocols.None` ，會讓 Kestrel 使用作業系統預設值選擇最適合的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="96bc2-209">The default value, `SslProtocols.None`, causes Kestrel to use the operating system defaults to choose the best protocol.</span></span> <span data-ttu-id="96bc2-210">除非您有特定的原因要選取通訊協定，否則請使用預設值。</span><span class="sxs-lookup"><span data-stu-id="96bc2-210">Unless you have a specific reason to select a protocol, use the default.</span></span>
## <a name="connection-logging"></a><span data-ttu-id="96bc2-211">連接記錄</span><span class="sxs-lookup"><span data-stu-id="96bc2-211">Connection logging</span></span>

<span data-ttu-id="96bc2-212">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="96bc2-212">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="96bc2-213">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="96bc2-213">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="96bc2-214">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="96bc2-214">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="96bc2-215">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="96bc2-215">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="96bc2-216">這是內建的 [連接中介軟體](#connection-middleware)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-216">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="96bc2-217">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="96bc2-217">Bind to a TCP socket</span></span>

<span data-ttu-id="96bc2-218"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="96bc2-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="96bc2-219">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="96bc2-219">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="96bc2-220">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="96bc2-220">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="96bc2-221">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="96bc2-221">Bind to a Unix socket</span></span>

<span data-ttu-id="96bc2-222">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="96bc2-222">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="96bc2-223">在 Nginx 設定檔中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-223">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="96bc2-224">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-224">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="96bc2-225">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-225">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="96bc2-226">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="96bc2-226">Port 0</span></span>

<span data-ttu-id="96bc2-227">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="96bc2-227">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="96bc2-228">下列範例示範如何判斷在執行時間系結的埠 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="96bc2-228">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="96bc2-229">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="96bc2-229">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="96bc2-230">限制</span><span class="sxs-lookup"><span data-stu-id="96bc2-230">Limitations</span></span>

<span data-ttu-id="96bc2-231">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="96bc2-231">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="96bc2-232">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="96bc2-232">`--urls` command-line argument</span></span>
* <span data-ttu-id="96bc2-233">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="96bc2-233">`urls` host configuration key</span></span>
* <span data-ttu-id="96bc2-234">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="96bc2-234">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="96bc2-235">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="96bc2-235">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="96bc2-236">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="96bc2-236">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="96bc2-237">除非在 HTTPS 端點設定中提供預設憑證，否則 HTTPS 無法與這些方法搭配使用 (例如，使用設定 `KestrelServerOptions` 或設定檔，如本文稍早所述) 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-237">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="96bc2-238">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="96bc2-238">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="96bc2-239">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="96bc2-239">IIS endpoint configuration</span></span>

<span data-ttu-id="96bc2-240">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="96bc2-240">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="96bc2-241">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-241">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="96bc2-242">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="96bc2-242">ListenOptions.Protocols</span></span>

<span data-ttu-id="96bc2-243">`Protocols` 屬性會建立在連線端點上或針對伺服器啟用的 HTTP 通訊協定 (`HttpProtocols`)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-243">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="96bc2-244">從 `HttpProtocols` 列舉中指派一個值給 `Protocols` 屬性。</span><span class="sxs-lookup"><span data-stu-id="96bc2-244">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="96bc2-245">`HttpProtocols` 列舉值</span><span class="sxs-lookup"><span data-stu-id="96bc2-245">`HttpProtocols` enum value</span></span> | <span data-ttu-id="96bc2-246">允許的連線通訊協定</span><span class="sxs-lookup"><span data-stu-id="96bc2-246">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="96bc2-247">僅限 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="96bc2-247">HTTP/1.1 only.</span></span> <span data-ttu-id="96bc2-248">可在具有或沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="96bc2-248">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="96bc2-249">僅限 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="96bc2-249">HTTP/2 only.</span></span> <span data-ttu-id="96bc2-250">只有在用戶端支援[先備知識模式](https://tools.ietf.org/html/rfc7540#section-3.4)時，才可以在沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="96bc2-250">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="96bc2-251">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="96bc2-251">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="96bc2-252">HTTP/2 要求用戶端在 TLS [應用層通訊協定協商 ](https://tools.ietf.org/html/rfc7301#section-3) 中選取 HTTP/2， (ALPN) 交握;否則，連接預設為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="96bc2-252">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="96bc2-253">`ListenOptions.Protocols`任何端點的預設值為 `HttpProtocols.Http1AndHttp2` 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-253">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="96bc2-254">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="96bc2-254">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="96bc2-255">TLS 1.2 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="96bc2-255">TLS version 1.2 or later</span></span>
* <span data-ttu-id="96bc2-256">已停用重新交涉</span><span class="sxs-lookup"><span data-stu-id="96bc2-256">Renegotiation disabled</span></span>
* <span data-ttu-id="96bc2-257">已停用壓縮</span><span class="sxs-lookup"><span data-stu-id="96bc2-257">Compression disabled</span></span>
* <span data-ttu-id="96bc2-258">暫時金鑰交換大小下限：</span><span class="sxs-lookup"><span data-stu-id="96bc2-258">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="96bc2-259">橢圓曲線 Diffie-Hellman (>ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; ：最少224位</span><span class="sxs-lookup"><span data-stu-id="96bc2-259">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="96bc2-260">有限的欄位 Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; ：最低2048位</span><span class="sxs-lookup"><span data-stu-id="96bc2-260">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="96bc2-261">不禁止加密套件。</span><span class="sxs-lookup"><span data-stu-id="96bc2-261">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="96bc2-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack;預設支援使用 P-256 橢圓曲線 &lbrack; `FIPS186` &rbrack; 。</span><span class="sxs-lookup"><span data-stu-id="96bc2-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="96bc2-263">下列範例會允許連接埠 8000 上的 HTTP/1.1 和 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="96bc2-263">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="96bc2-264">這些連線使用提供的憑證受到 TLS 保護：</span><span class="sxs-lookup"><span data-stu-id="96bc2-264">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="96bc2-265">在 Linux 上， <xref:System.Net.Security.CipherSuitesPolicy> 可用來篩選每個連接的 TLS 交握：</span><span class="sxs-lookup"><span data-stu-id="96bc2-265">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

## <a name="connection-middleware"></a><span data-ttu-id="96bc2-266">連接中介軟體</span><span class="sxs-lookup"><span data-stu-id="96bc2-266">Connection Middleware</span></span>

<span data-ttu-id="96bc2-267">如有需要，自訂連線中介軟體可以針對特定加密，篩選每個連線的 TLS 交握。</span><span class="sxs-lookup"><span data-stu-id="96bc2-267">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="96bc2-268">下列範例 <xref:System.NotSupportedException> 會針對應用程式不支援的任何加密演算法擲回。</span><span class="sxs-lookup"><span data-stu-id="96bc2-268">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="96bc2-269">或者，將 [ITlsHandshakeFeature](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 定義和比較成可接受的加密套件清單。</span><span class="sxs-lookup"><span data-stu-id="96bc2-269">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="96bc2-270">CipherAlgorithmType 不會使用加密 [。 Null](xref:System.Security.Authentication.CipherAlgorithmType) 加密演算法。</span><span class="sxs-lookup"><span data-stu-id="96bc2-270">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="96bc2-271">您也可以透過 lambda 設定連接篩選 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="96bc2-271">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="96bc2-272">從 configuration 設定 HTTP 通訊協定</span><span class="sxs-lookup"><span data-stu-id="96bc2-272">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="96bc2-273">`CreateDefaultBuilder` 預設會呼叫 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="96bc2-273">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="96bc2-274">下列 *appsettings.json* 範例會建立 HTTP/1.1 作為所有端點的預設連接通訊協定：</span><span class="sxs-lookup"><span data-stu-id="96bc2-274">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="96bc2-275">下列 *appsettings.json* 範例會建立特定端點的 HTTP/1.1 連接通訊協定：</span><span class="sxs-lookup"><span data-stu-id="96bc2-275">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="96bc2-276">程式碼中指定的通訊協定會覆寫設定所設定的值。</span><span class="sxs-lookup"><span data-stu-id="96bc2-276">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="96bc2-277">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="96bc2-277">URL prefixes</span></span>

<span data-ttu-id="96bc2-278">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="96bc2-278">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="96bc2-279">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="96bc2-279">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="96bc2-280">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="96bc2-280">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="96bc2-281">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="96bc2-281">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="96bc2-282">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="96bc2-282">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="96bc2-283">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="96bc2-283">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="96bc2-284">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="96bc2-284">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="96bc2-285">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="96bc2-285">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="96bc2-286">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="96bc2-286">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="96bc2-287">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="96bc2-287">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="96bc2-288">若要在相同埠上將不同的主機名稱系結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="96bc2-288">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="96bc2-289">反向 proxy 伺服器範例包括 IIS、Nginx 或 Apache。</span><span class="sxs-lookup"><span data-stu-id="96bc2-289">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="96bc2-290">裝載於反向 Proxy 組態需要[主機篩選](xref:fundamentals/servers/kestrel/host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="96bc2-290">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="96bc2-291">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="96bc2-291">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="96bc2-292">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="96bc2-292">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="96bc2-293">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="96bc2-293">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="96bc2-294">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="96bc2-294">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
