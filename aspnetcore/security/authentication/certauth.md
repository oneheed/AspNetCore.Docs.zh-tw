---
title: 在 ASP.NET Core 中設定憑證驗證
author: blowdart
description: 瞭解如何在 ASP.NET Core 中設定 IIS 和 HTTP.sys 的憑證驗證。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: 57d46e34993148943b1e9680a372405be9c80605
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424200"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>在 ASP.NET Core 中設定憑證驗證

`Microsoft.AspNetCore.Authentication.Certificate` 包含類似于 ASP.NET Core [憑證驗證](https://tools.ietf.org/html/rfc5246#section-7.4.4) 的實作為。 憑證驗證會在 TLS 層級進行，但在達到 ASP.NET Core 之前也是如此。 更精確地說，這是驗證憑證的驗證處理常式，然後為您提供可將該憑證解析為的事件 `ClaimsPrincipal` 。 

將[您的伺服器設定](#configure-your-server-to-require-certificates)為使用 IIS、Kestrel、Azure Web Apps，或您所使用的任何其他伺服器進行憑證驗證。

## <a name="proxy-and-load-balancer-scenarios"></a>Proxy 和負載平衡器案例

憑證驗證是一種可設定狀態的案例，主要用於 proxy 或負載平衡器不會處理用戶端與伺服器之間的流量。 如果使用 proxy 或負載平衡器，憑證驗證只適用于 proxy 或負載平衡器：

* 處理驗證。
* 將使用者驗證資訊傳遞給應用程式 (例如，在要求標頭中，) ，其會對驗證資訊採取行動。

使用 proxy 和負載平衡器的環境中的憑證驗證替代方法是使用 OpenID Connect (OIDC) Active Directory 同盟服務 (ADFS) 。

## <a name="get-started"></a>開始使用

取得 HTTPS 憑證、加以套用，並 [設定您的伺服器](#configure-your-server-to-require-certificates) 以要求憑證。

在您的 web 應用程式中，新增 AspNetCore 的參考。 [憑證](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) 套件。 然後，在 `Startup.ConfigureServices` 方法中， `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` 使用您的選項呼叫，提供的委派， `OnCertificateValidated` 以在隨要求傳送的用戶端憑證上進行任何補充驗證。 請將該資訊轉換成 `ClaimsPrincipal` ，並在屬性上設定它 `context.Principal` 。

如果驗證失敗，此處理程式 `403 (Forbidden)` `401 (Unauthorized)` 會如您所預期般傳迴響應，而不是。 原因是必須在初始 TLS 連接期間進行驗證。 到達處理常式時，時間太晚。 沒有任何方法可將連線從匿名連接升級為具有憑證的連接。

此外，也要加入 `app.UseAuthentication();` `Startup.Configure` 方法。 否則， `HttpContext.User` 將不會設定為 `ClaimsPrincipal` 從憑證建立。 例如：

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

上述範例示範新增憑證驗證的預設方式。 處理常式會使用一般憑證屬性來建立使用者主體。

## <a name="configure-certificate-validation"></a>設定憑證驗證

`CertificateAuthenticationOptions`處理常式具有一些內建驗證，這是您應該對憑證執行的最小驗證。 預設會啟用這些設定。

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = 鏈式、SelfSigned 或 All (連鎖 |SelfSigned) 

預設值：`CertificateTypes.Chained`

這項檢查會驗證是否只允許適當的憑證類型。 如果應用程式使用自我簽署憑證，則必須將此選項設定為 `CertificateTypes.All` 或 `CertificateTypes.SelfSigned` 。

### <a name="validatecertificateuse"></a>ValidateCertificateUse

預設值：`true`

這項檢查會驗證用戶端出示的憑證是否有用戶端驗證擴充金鑰使用 (EKU) ，或完全沒有 Eku。 如同規定，如果未指定 EKU，則所有的 Eku 都會被視為有效。

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

預設值：`true`

這項檢查會驗證憑證是否在其有效期間內。 在每個要求中，此處理程式可確保在其目前的會話中呈現時，憑證的有效期限未過期。

### <a name="revocationflag"></a>RevocationFlag

預設值：`X509RevocationFlag.ExcludeRoot`

旗標，指定要檢查鏈中的哪些憑證以進行撤銷。

只有當憑證連結到根憑證時，才會執行撤銷檢查。

### <a name="revocationmode"></a>RevocationMode

預設值：`X509RevocationMode.Online`

指定如何執行撤銷檢查的旗標。

指定線上檢查可能會在與憑證授權單位單位聯繫時造成長時間延遲。

只有當憑證連結到根憑證時，才會執行撤銷檢查。

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>我可以將應用程式設定為只需要特定路徑上的憑證嗎？

這是不可能的。 請記住，憑證交換是在 HTTPS 對話的開頭完成，伺服器會在該連接上收到第一個要求之前完成，因此無法根據任何要求欄位進行範圍。

## <a name="handler-events"></a>處理程式事件

處理常式有兩個事件：

* `OnAuthenticationFailed`：如果在驗證期間發生例外狀況，並可讓您進行回應，則呼叫。
* `OnCertificateValidated`：憑證經過驗證之後呼叫，通過驗證和預設主體已建立。 此事件可讓您執行自己的驗證，並增強或取代主體。 範例包括：
  * 判斷您的服務是否知道憑證。
  * 建立您自己的主體。 請考慮 `Startup.ConfigureServices` 中的下列範例：

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

如果您發現輸入憑證不符合額外的驗證，請呼叫 `context.Fail("failure reason")` 失敗原因。

對於真正的功能，您可能會想要呼叫在相依性插入中註冊的服務，以連接到資料庫或其他類型的使用者存放區。 使用傳遞至委派的內容來存取您的服務。 請考慮 `Startup.ConfigureServices` 中的下列範例：

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

就概念而言，憑證的驗證是授權的考慮。 例如，在授權原則中（而非內部）新增檢查， `OnCertificateValidated` 是完全可接受的。

## <a name="configure-your-server-to-require-certificates"></a>設定您的伺服器以要求憑證

### <a name="kestrel"></a>Kestrel

在 *Program.cs*中，設定 Kestrel，如下所示：

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> 在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。

### <a name="iis"></a>IIS

在 IIS 管理員中完成下列步驟：

1. 從 [ **連接** ] 索引標籤中選取您的網站。
1. 在 [**功能] 視圖**視窗中，按兩下 [ **SSL 設定**] 選項。
1. 選取 [**需要 SSL** ] 核取方塊，然後在 [**用戶端憑證**] 區段中選取 [**需要**] 選項按鈕。

![IIS 中的用戶端憑證設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure 和自訂 web proxy

如需如何設定憑證轉送中介軟體的詳細說明，請參閱 [主機和部署檔](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) 。

### <a name="use-certificate-authentication-in-azure-web-apps"></a>在 Azure Web Apps 中使用憑證驗證

Azure 不需要轉送設定。 這已在憑證轉送中介軟體中設定。

> [!NOTE]
> 這需要有 CertificateForwardingMiddleware。

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>在自訂 web proxy 中使用憑證驗證

`AddCertificateForwarding`方法是用來指定：

* 用戶端標頭名稱。
* 如何使用屬性) 載入憑證 (`HeaderConverter` 。

例如，在自訂 web proxy 中，會將憑證作為自訂要求標頭來傳遞 `X-SSL-CERT` 。 若要使用它，請在中設定憑證轉送 `Startup.ConfigureServices` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

`Startup.Configure`然後，方法會新增中介軟體。 `UseCertificateForwarding` 呼叫和之前呼叫 `UseAuthentication` `UseAuthorization` ：

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

您可以使用不同的類別來執行驗證邏輯。 因為此範例中使用的是相同的自我簽署憑證，請確定只可使用您的憑證。 驗證用戶端憑證和伺服器憑證的指紋是否相符，否則任何憑證都可供使用，並且足以進行驗證。 這會在方法內使用 `AddCertificate` 。 如果您使用的是中繼憑證或子系憑證，您也可以在此處驗證主體或簽發者。

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>使用憑證和 HttpClientHandler 來執行 HttpClient

`HttpClientHandler`可以直接加入類別的函式中 `HttpClient` 。 建立的實例時，請務必小心 `HttpClient` 。 `HttpClient`然後，會將憑證傳送給每個要求。

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>使用憑證和 IHttpClientFactory 中的命名 HttpClient 來執行 HttpClient 

在下列範例中，會 `HttpClientHandler` 使用處理程式中的屬性，將用戶端憑證新增至 `ClientCertificates` 。 然後，您可以使用方法，在的已命名實例中使用此處理程式 `HttpClient` `ConfigurePrimaryHttpMessageHandler` 。 這是在中設定 `Startup.ConfigureServices` ：

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

`IHttpClientFactory`然後可以用來取得具有處理常式和憑證的已命名實例。 `CreateClient`具有類別中定義之用戶端名稱的方法 `Startup` 會用來取得實例。 您可以視需要使用用戶端來傳送 HTTP 要求。

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

如果將正確的憑證傳送至伺服器，就會傳回資料。 如果未傳送憑證或錯誤的憑證，則會傳回 HTTP 403 狀態碼。

### <a name="create-certificates-in-powershell"></a>在 PowerShell 中建立憑證

建立憑證是設定此流程最困難的部分。 您可以使用 PowerShell Cmdlet 來建立根憑證 `New-SelfSignedCertificate` 。 建立憑證時，請使用強式密碼。 請務必新增 `KeyUsageProperty` 參數和 `KeyUsage` 參數，如下所示。

#### <a name="create-root-ca"></a>建立根 CA

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> `-DnsName`參數值必須符合應用程式的部署目標。 例如，"localhost" 用於開發。

#### <a name="install-in-the-trusted-root"></a>在信任的根目錄中安裝

您的主機系統上必須信任根憑證。 預設不會信任憑證授權單位單位所建立的根憑證。 下列連結說明如何在 Windows 上完成這項作業：

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>中繼憑證

您現在可以從根憑證建立中繼憑證。 並非所有使用案例都需要此項，但您可能需要建立許多憑證，或需要啟用或停用憑證群組。 `TextExtension`需要參數才能在憑證的基本條件約束中設定路徑長度。

中繼憑證接著可以新增至 Windows 主機系統中受信任的中繼憑證。

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>從中繼憑證建立子憑證

您可以從中繼憑證建立子系憑證。 這是終端實體，不需要建立更多子系憑證。

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>從根憑證建立子憑證

您也可以直接從根憑證建立子系憑證。 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>範例跟中繼憑證-憑證

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

使用根、中繼或子系憑證時，您可以視需要使用指紋或 PublicKey 來驗證憑證。

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>憑證驗證快取

ASP.NET Core 5.0 和更新版本支援啟用驗證結果快取的功能。 快取可大幅改善憑證驗證的效能，因為驗證是昂貴的作業。

根據預設，憑證驗證會停用快取。 若要啟用快取，請 `AddCertificateCache` 在中呼叫 `Startup.ConfigureServices` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

預設的快取執行會將結果儲存在記憶體中。 您可以藉由使用相依性插入來執行和註冊，來提供您自己的快取 `ICertificateValidationCache` 。 例如： `services.AddSingleton<ICertificateValidationCache, YourCache>()` 。

::: moniker-end

## <a name="optional-client-certificates"></a>選用用戶端憑證

本節提供的資訊適用于必須使用憑證來保護應用程式子集的應用程式。 例如， Razor 應用程式中的頁面或控制器可能需要用戶端憑證。 這會以用戶端憑證的形式呈現挑戰：
  
* 是 TLS 功能，而不是 HTTP 功能。
* 會依連線進行協商，而且必須在任何 HTTP 資料可用之前，于連接開始時進行協商。 在連接開始時，只會知道伺服器名稱指示 (SNI) &dagger; 。 用戶端和伺服器憑證會在連線上的第一個要求之前進行協商，要求通常無法重新進行協商。

TLS 重新協商是執行選用用戶端憑證的舊方式。 因為下列原因，所以不再建議您這樣做：
- 在 HTTP/1.1 中，POST 要求期間的重新交涉可能會造成鎖死，要求主體填滿 TCP 視窗，而且無法接收重新協商封包。
- HTTP/2 [明確禁止](https://tools.ietf.org/html/rfc7540#section-9.2.1) 重新協商。
- TLS 1.3 已 [移除](https://tools.ietf.org/html/rfc8740#section-1) 重新協商的支援。

ASP.NET Core 5 preview 7 和更新版本為選用用戶端憑證新增更便利的支援。 如需詳細資訊，請參閱 [選用的憑證範例](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample)。

下列方法支援選用用戶端憑證：

* 設定網域和子域的系結：
  * 例如，在和上設定系 `contoso.com` 結 `myClient.contoso.com` 。 `contoso.com`主機不需要用戶端憑證，而是 `myClient.contoso.com` 。
  * 如需詳細資訊，請參閱
    * [Kestrel](/fundamentals/servers/kestrel)：
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * 注意 Kestrel 目前不支援一個系結上的多個 TLS 設定，您需要兩個具有唯一 Ip 或埠的系結。 請參閱https://github.com/dotnet/runtime/issues/31097
    * IIS
      * [裝載 IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [在 IIS 上設定安全性](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys： [設定 Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)
* 針對需要用戶端憑證且沒有用戶端憑證的 web 應用程式要求：
  * 使用受用戶端憑證保護的子域重新導向至相同的頁面。
  * 例如，重新導向至 `myClient.contoso.com/requestedPage` 。 因為的要求與 `myClient.contoso.com/requestedPage` 不同的主機名稱 `contoso.com/requestedPage` ，用戶端會建立不同的連線，並提供用戶端憑證。
  * 如需詳細資訊，請參閱<xref:security/authorization/introduction>。

針對 [此 GitHub 討論](https://github.com/dotnet/AspNetCore.Docs/issues/18720) 問題中的選用用戶端憑證留下問題、留言和其他意見反應。

&dagger; 伺服器名稱指示 (SNI) 是 TLS 延伸模組，可將虛擬網域納入 SSL 協商的一部分。 這實際上是指虛擬功能變數名稱（或主機名稱）可以用來識別網路端點。
