---
title: ASP.NET Core 5.0 的新功能
author: rick-anderson
description: 瞭解 ASP.NET Core 5.0 中的新功能。
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: d7ffcb67637593ab2909885a9e1f6de74a78361b
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855491"
---
# <a name="whats-new-in-aspnet-core-50"></a>ASP.NET Core 5.0 的新功能

本文將重點說明 ASP.NET Core 5.0 中最重要的變更，以及相關檔的連結。

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC 和 Razor 改進

### <a name="model-binding-datetime-as-utc"></a>模型系結日期時間（UTC）

模型系結現在支援將 UTC 時間字串系結至 `DateTime` 。 如果要求包含 UTC 時間字串，則模型系結會將它系結至 UTC `DateTime` 。 例如，下列時間字串系結 UTC `DateTime` ： `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>使用 c # 9 記錄類型進行模型系結和驗證

[C # 9 記錄類型](/dotnet/csharp/whats-new/csharp-9#record-types) 可以搭配 MVC 控制器或頁面中的模型系結使用 Razor 。 記錄類型是在網路上傳送資料模型的好方式。

例如，下列 `PersonController` 使用 `Person` 具有模型系結和表單驗證的記錄類型：

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

`Person/Index.cshtml` 檔案：

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>DynamicRouteValueTransformer 的改善

ASP.NET Core 3.1 引進 <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> 了一種方式，可讓您使用自訂端點，以動態方式選取 MVC 控制器動作或 Razor 頁面。 ASP.NET Core 5.0 應用程式可以將狀態傳遞至 `DynamicRouteValueTransformer` ，並篩選所選擇的端點集合。

### <a name="miscellaneous"></a>其他

* [[比較]](xref:System.ComponentModel.DataAnnotations.CompareAttribute)屬性可以套用至頁面模型上的屬性 Razor 。
* 預設會將系結的參數和屬性視為必要。 <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Web API

### <a name="openapi-specification-on-by-default"></a>預設為開啟 OpenAPI 規格

[OpenAPI 規格](http://spec.openapis.org/oas/v3.0.3) 是一種業界標準，用於描述 HTTP api，並將其整合至複雜的商務程式或協力廠商。 所有雲端提供者和許多 API 登錄都廣泛支援 OpenAPI。 從 web Api 發出 OpenAPI 檔的應用程式有各種新的機會，可讓您使用這些 Api。 與開放原始碼專案 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)的維護人員合作，ASP.NET Core API 範本包含 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)的 NuGet 相依性。 Swashbuckle 是常用的開放原始碼 NuGet 套件，可動態發出 OpenAPI 的檔。 Swashbuckle 藉由 introspecting API 控制器，並在執行時間產生 OpenAPI 檔，或在組建階段使用 Swashbuckle CLI 來執行此工作。

在 ASP.NET Core 5.0 中，web API 範本預設會啟用 OpenAPI 支援。 若要停用 OpenAPI：

* 從命令列：

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* 從 Visual Studio：取消核取 [ **啟用 OpenAPI 支援**]。

針對 web API 專案所建立的所有 *.csproj* 檔案都包含 [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet 套件參考。

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

範本產生的程式碼包含中 `Startup.ConfigureServices` 的程式碼，可啟用 OpenAPI 檔產生：

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

`Startup.Configure`方法會新增 Swashbuckle 中介軟體，以啟用：

* 檔產生程式。
* 在開發模式中，預設為 Swagger UI 頁面。

範本產生的程式碼在發行至生產環境時，不會意外地公開 API 的描述。

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Azure API 管理匯入

ASP.NET Core API 專案啟用 OpenAPI 時，Visual Studio 2019 16.8 版和更新版本的發行會自動在發佈流程中提供額外的步驟。 使用 [AZURE API 管理](xref:tutorials/publish-to-azure-api-management-using-vs) 的開發人員有機會在發佈流程期間，自動將 api 匯入至 Azure api 管理：

![Azure API 管理匯入與發佈](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>更好的 web API 專案啟動體驗

OpenAPI 預設為啟用，web API 開發人員的應用程式啟動體驗 (F5) 可大幅改善。 使用 ASP.NET Core 5.0 時，會預先設定 web API 範本以載入 Swagger UI 頁面。 Swagger UI 頁面提供針對已發佈 API 新增的檔，並可讓您按一下就能測試 Api。

![swagger/index.html 視圖](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>效能改善

針對 .NET 5，我們對執行時間效能進行了大幅改善， Blazor WebAssembly 並將焦點放在複雜的 UI 轉譯和 JSON 序列化。 在我們的效能測試中， Blazor WebAssembly 在大部分情況下，.net 5 的速度會快二到三倍。 如需詳細資訊，請參閱 [ASP.NET Blog： .net 5 候選版1中的 ASP.NET Core 更新](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements)。

### <a name="css-isolation"></a>CSS 隔離

Blazor 現在支援定義範圍設定為特定元件的 CSS 樣式。 元件專屬的 CSS 樣式可讓您更輕鬆地瞭解應用程式中的樣式，並避免全域樣式的意外副作用。 如需詳細資訊，請參閱<xref:blazor/components/css-isolation>。

### <a name="new-inputfile-component"></a>新增 `InputFile` 元件

`InputFile`元件允許讀取使用者所選取的一或多個檔案進行上傳。 如需詳細資訊，請參閱<xref:blazor/file-uploads>。

### <a name="new-inputradio-and-inputradiogroup-components"></a>新 `InputRadio` 的和 `InputRadioGroup` 元件

Blazor 具有內建 `InputRadio` 和 `InputRadioGroup` 元件，可簡化資料系結至具有整合式驗證的選項按鈕群組。 如需詳細資訊，請參閱<xref:blazor/forms-validation>。

### <a name="component-virtualization"></a>元件虛擬化

使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。 如需詳細資訊，請參閱<xref:blazor/components/virtualization>。

### <a name="ontoggle-event-support"></a>`ontoggle` 事件支援

Blazor 事件現在支援 `ontoggle` DOM 事件。 如需詳細資訊，請參閱<xref:blazor/components/event-handling#event-argument-types>。

### <a name="set-ui-focus-in-no-locblazor-apps"></a>在應用程式中設定 UI 焦點 Blazor

`FocusAsync`在元素參考上使用便利方法，以將 UI 焦點設定為該專案。 如需詳細資訊，請參閱<xref:blazor/components/event-handling#focus-an-element>。

### <a name="custom-validation-class-attributes"></a>自訂驗證類別屬性

在與 CSS 架構（例如啟動程式）整合時，自訂驗證類別名稱很有用。 如需詳細資訊，請參閱<xref:blazor/forms-validation#custom-validation-class-attributes>。

### <a name="iasyncdisposable-support"></a>System.iasyncdisposable 支援

Blazor 元件現在支援已配置 <xref:System.IAsyncDisposable> 資源之非同步版本的介面。

### <a name="javascript-isolation-and-object-references"></a>JavaScript 隔離和物件參考

Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。 如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。

### <a name="form-components-support-display-name"></a>表單元件支援顯示名稱

下列內建元件支援使用參數顯示名稱 `DisplayName` ：

* `InputDate`
* `InputNumber`
* `InputSelect`

如需詳細資訊，請參閱<xref:blazor/forms-validation#display-name-support>。

### <a name="catch-all-route-parameters"></a>Catch-all 路由參數

元件中支援攔截所有路由參數，這些參數會跨多個資料夾界限來捕捉路徑。 如需詳細資訊，請參閱<xref:blazor/fundamentals/routing#catch-all-route-parameters>。

### <a name="debugging-improvements"></a>偵錯改進

Blazor WebAssemblyASP.NET Core 5.0 中已改善偵錯工具的功能。 此外，Visual Studio for Mac 上現在支援偵錯工具。 如需詳細資訊，請參閱<xref:blazor/debug>。

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft v2.0 Identity 和 MSAL 2.0 版

Blazor 安全性現在使用 Microsoft v2.0 Identity ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) 以及 [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) 和 MSAL 2.0 版。 如需詳細資訊，請參閱[ Blazor 安全性和 Identity 節點](xref:blazor/security/index)中的主題。

### <a name="protected-browser-storage-for-no-locblazor-server"></a>受保護的瀏覽器存放裝置 Blazor Server

Blazor Server 應用程式現在可以使用內建的支援，在瀏覽器中儲存應用程式狀態，以防止使用 ASP.NET Core 資料保護進行篡改。 資料可以儲存在本機瀏覽器儲存體或會話儲存體中。 如需詳細資訊，請參閱<xref:blazor/state-management>。

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly 預

跨裝載模型改善了元件整合，而 Blazor WebAssembly 應用程式現在可以在伺服器上呈現輸出。 <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>修剪/連結改進

Blazor WebAssembly 在組建期間執行中繼語言 (IL) 修剪/連結，以從應用程式的輸出元件中修剪不必要的 IL。 在 ASP.NET Core 5.0 的版本中，會 Blazor WebAssembly 使用其他設定選項來執行改良的修剪。 如需詳細資訊，請參閱 <xref:blazor/host-and-deploy/configure-trimmer> 和 [修剪選項](/dotnet/core/deploying/trimming-options)。

### <a name="browser-compatibility-analyzer"></a>瀏覽器相容性分析器

Blazor WebAssembly 應用程式會以完整的 .NET API 介面區為目標，但由於瀏覽器沙箱條件約束，WebAssembly 並不支援所有的 .NET Api。 <xref:System.PlatformNotSupportedException>在 WebAssembly 上執行時，不支援的 api 會擲回。 當應用程式使用應用程式的目標平臺不支援的 Api 時，平臺相容性分析器會警告開發人員。 如需詳細資訊，請參閱<xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>。

### <a name="lazy-load-assemblies"></a>延遲載入元件

Blazor WebAssembly 應用程式啟動效能可透過延後載入部分應用程式元件來改善，直到需要它們為止。 如需詳細資訊，請參閱<xref:blazor/webassembly-lazy-load-assemblies>。

### <a name="updated-globalization-support"></a>更新的全球化支援

全球化支援可 Blazor WebAssembly 根據 Unicode (ICU) 的國際元件來提供。 如需詳細資訊，請參閱<xref:blazor/globalization-localization>。

## <a name="grpc"></a>gRPC

已在 [gRPC](https://grpc.io/)中進行許多效能改進。 如需詳細資訊，請參閱 [.net 5 中的 gRPC 效能改進](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/)。

如需 gRPC 的詳細資訊，請參閱 <xref:grpc/index> 。

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR 中樞篩選

SignalR 中樞篩選（在 ASP.NET 中稱為中樞管線 SignalR ）是一項功能，可讓程式碼在呼叫中樞方法之前和之後執行。 呼叫中樞方法之前和之後執行程式碼，類似于中介軟體在 HTTP 要求之前和之後執行程式碼的能力。 常見用途包括記錄、錯誤處理和引數驗證。

如需詳細資訊，請參閱[使用 ASP.NET Core SignalR 中的中樞篩選](xref:signalr/hub-filters)。

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR 平行中樞調用

ASP.NET Core SignalR 現在能夠處理平行中樞調用。 您可以變更預設行為，讓用戶端一次叫用一個以上的中樞方法：

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>已新增 SignalR JAVA 用戶端的 Messagepack 支援

新的封裝 [signalr messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)會將 messagepack 支援新增至 SignalR JAVA 用戶端。 若要使用 MessagePack 中樞通訊協定，請新增 `.withHubProtocol(new MessagePackHubProtocol())` 至連接產生器：

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* 透過設定可重載端點： Kestrel 可以偵測傳遞給[ KestrelServerOptions.Configu) ](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A)的設定變更，以及系結至新的端點，而不需要在參數為時重新開機應用程式 `reloadOnChange` `true` 。 使用或時，預設會系結 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> Kestrel 至 [ Kestrel ](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) `reloadOnChange` 已啟用的「」設定子區段。 應用程式必須 `reloadOnChange: true` 在 `KestrelServerOptions.Configure` 手動呼叫以取得可重載端點時傳遞。
* HTTP/2 回應標頭改善。 如需詳細資訊，請參閱下一節中的 [效能改進](#performance-improvements) 。
* 支援通訊端傳輸中的其他端點類型：加入至引進的新 API <xref:System.Net.Sockets?displayProperty=nameWithType> ，中的通訊端預設傳輸 [Kestrel](xref:fundamentals/servers/kestrel) 允許系結至現有的檔案控制代碼和 Unix 網域通訊端。 支援系結至現有的檔案控制代碼可讓您使用現有的 `Systemd` 整合，而不需要 `libuv` 傳輸。
* 中的自訂標頭解碼 Kestrel ：應用程式可以指定要 <xref:System.Text.Encoding> 根據標頭名稱（而非預設為 utf-8）來解讀傳入標頭。 設定 <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` 屬性，指定要使用的編碼：
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Kestrel 透過設定的端點特定選項

已新增支援，可透過設定來設定 Kestrel 的端點特定[configuration](xref:fundamentals/configuration/index)選項。 端點特定的設定包括：

* 使用的 HTTP 通訊協定
* 使用的 TLS 通訊協定
* 選取的憑證
* 用戶端憑證模式

設定可讓您根據指定的伺服器名稱來指定所選取的憑證。 伺服器名稱屬於 TLS 通訊協定伺服器名稱指示 (SNI) 擴充功能的一部分，如用戶端所指示。 Kestrel的設定也支援主機名稱中的萬用字元前置詞。

下列範例示範如何使用設定檔來指定端點專用：

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

伺服器名稱指示 (SNI) 是 TLS 延伸模組，可將虛擬網域納入 SSL 協商的一部分。 這實際上是指虛擬功能變數名稱（或主機名稱）可以用來識別網路端點。

## <a name="performance-improvements"></a>效能改善

### <a name="http2"></a>HTTP/2

* 大幅減少 HTTP/2 程式碼路徑中的配置。
* 支援在中 HPack HTTP/2 回應標頭的 [動態壓縮](https://tools.ietf.org/html/rfc7541) [Kestrel](xref:fundamentals/servers/kestrel) 。 如需詳細資訊，請參閱 [標頭資料表大小](xref:fundamentals/servers/kestrel#header-table-size) 和 [HPACK： HTTP/2 的無訊息無訊息 (功能) ](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/)。
* 傳送 HTTP/2 偵測框架： HTTP/2 有傳送偵測框架的機制，以確保閒置的連接仍可正常運作。 當處理經常閒置但只間歇性看到活動（例如 gRPC 串流）的長期資料流程時，確保可行的連接特別有用。 應用程式可以在中設定限制來傳送定期偵測框架 [Kestrel](xref:fundamentals/servers/kestrel) <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> ：

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>容器

在 .NET 5.0 之前，為 ASP.NET Core 應用程式建立和發佈 *Dockerfile* 需要提取整個 .NET Core SDK 和 ASP.NET Core 映射。 在此版本中，會減少提取 SDK 映射位元組，並大幅消除 ASP.NET Core 映射提取的位元組。 如需詳細資訊，請參閱 [此 GitHub 問題批註](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)。

## <a name="authentication-and-authorization"></a>驗證與授權

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>使用 Microsoft 驗證 Identity Azure Active Directory。Web

ASP.NET Core 專案範本現在會與整合， <xref:Microsoft.Identity.Web?displayProperty=fullName> 以使用 [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) 來處理驗證。 [Microsoft ... IdentityWeb 套件](https://www.nuget.org/packages/Microsoft.Identity.Web/)提供：

* 透過 Azure AD 進行驗證的更佳體驗。
* 代表您的使用者輕鬆存取 Azure 資源的方式，包括 [Microsoft Graph](/graph/overview)。 請參閱 [Microsoft ... IdentityWeb 範例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)，使用 Azure api、使用 Microsoft Graph 和保護您自己的 api，從基本登入開始，並透過多租使用者前進。 `Microsoft.Identity.Web` 可與 .NET 5 一起使用。

### <a name="allow-anonymous-access-to-an-endpoint"></a>允許匿名存取端點

`AllowAnonymous`擴充方法允許匿名存取端點：

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>授權失敗的自訂處理

透過[授權](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A)[中介軟體](xref:fundamentals/middleware/index)所叫用的新[IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs)介面，現在可以更輕鬆地自訂授權失敗的處理。 預設的執行會維持不變，但自訂處理常式可以在 [相依性插入] 中註冊，以根據授權失敗的原因來允許自訂 HTTP 回應。 請參閱 [此範例](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) ，以示範的使用方式 `IAuthorizationMiddlewareResultHandler` 。

### <a name="authorization-when-using-endpoint-routing"></a>使用端點路由時的授權

使用端點路由時的授權，現在會接收 `HttpContext` 而不是端點實例。 這可讓授權中介軟體存取 `RouteData` 無法透過類別存取之的和其他屬性 `HttpContext` <xref:Microsoft.AspNetCore.Http.Endpoint> 。 您可以使用內容從內容提取端點 [。GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A)。

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>以角色為基礎的存取控制與 Kerberos 驗證，以及 Linux 上的 LDAP

請參閱 [Kerberos 驗證和角色型存取控制 (RBAC) ](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>API 改進

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>HttpRequest 和 HttpResponse 的 JSON 擴充方法

您可以從 `HttpRequest` 和 `HttpResponse` 使用新的 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 和 `WriteAsJsonAsync` 擴充方法，讀取和寫入 JSON 資料。 這些擴充方法會使用序列化程式 [ 上的System.Text.Js](xref:System.Text.Json) 來處理 JSON 資料。 新的 `HasJsonContentType` 擴充方法也可以檢查要求是否有 JSON 內容類型。

JSON 擴充方法可以與 [端點路由](xref:fundamentals/routing) 結合，以我們呼叫 ***route 至 code** _ 的程式設計風格來建立 JSON api。 如果開發人員想要以輕量方式建立基本的 JSON Api，這是新的選項。 例如，只有少數幾個端點的 web 應用程式，可能會選擇使用路由傳送至程式碼，而不是 ASP.NET Core MVC 的完整功能：

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

如需有關新的 JSON 擴充方法和程式碼路由的詳細資訊，請參閱 [這個 .net 節目](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code)。

### <a name="systemdiagnosticsactivity"></a>系統診斷。活動

預設格式現在預設 <xref:System.Diagnostics.Activity?displayProperty=fullName> 為 W3C 格式。 這使得 ASP.NET Core 中的分散式追蹤支援，在預設情況下可與更多架構互通。

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> 現在支援設定可將這些參數或屬性視為選擇性的選項：

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>其他改進

我們已開始將 [可為 null 的注釋](/dotnet/csharp/nullable-references#attributes-describe-apis) 套用至 ASP.NET Core 元件。 我們計畫對大部分的 .NET 5 framework 公用 API 介面加上批註。 <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>控制啟動類別啟用

已新增額外的多載 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> ，可讓應用程式提供 factory 方法來控制 `Startup` 類別啟用。 控制 `Startup` 類別啟用有助於傳遞與 `Startup` 主機一起初始化的其他參數：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>使用 dotnet 監看自動重新整理

在 .NET 5 中，在 ASP.NET Core 專案上執行 [dotnet watch](xref:tutorials/dotnet-watch) 都會啟動預設的瀏覽器，並在對程式碼進行變更時自動重新整理瀏覽器。 這表示您可以：

_ 在文字編輯器中開啟 ASP.NET Core 專案。
* 執行 `dotnet watch`。
* 將焦點放在程式碼變更時，工具會處理重建、重新開機和重載應用程式。

### <a name="console-logger-formatter"></a>主控台記錄器格式器

程式庫中的主控台記錄提供者已進行改進 `Microsoft.Extensions.Logging` 。 開發人員現在可以執行自訂的 `ConsoleFormatter` ，以對主控台輸出的格式化和顏色標示進行完整的控制。 格式器 Api 可執行 VT-100 escape 序列的子集，以提供豐富的格式設定。 大部分新式終端都支援 VT-100。 主控台記錄器可以在不支援的終端上剖析出 escape 序列，讓開發人員能夠為所有終端機撰寫單一格式器。

### <a name="json-console-logger"></a>JSON 主控台記錄器

除了支援自訂格式器之外，我們也新增了內建的 JSON 格式器，可將結構化的 JSON 記錄發出至主控台。 下列程式碼示範如何從預設記錄器切換至 JSON：

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

發出至主控台的記錄訊息是 JSON 格式：

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
