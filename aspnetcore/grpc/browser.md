---
title: 在瀏覽器應用程式中使用 gRPC
author: jamesnk
description: 瞭解如何在 ASP.NET Core 上設定 gRPC services，以便從使用 gRPC 的瀏覽器應用程式呼叫。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058905"
---
# <a name="use-grpc-in-browser-apps"></a>在瀏覽器應用程式中使用 gRPC

依 [James 牛頓](https://twitter.com/jamesnk)

 瞭解如何使用 [GRPC Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) 通訊協定，將現有的 ASP.NET Core gRPC 服務設定為可從瀏覽器應用程式呼叫。 gRPC Web 可讓瀏覽器 JavaScript 和 Blazor 應用程式呼叫 gRPC services。 您無法從瀏覽器型應用程式呼叫 HTTP/2 gRPC 服務。 ASP.NET Core 中裝載的 gRPC 服務可以設定為支援 gRPC Web 和 HTTP/2 gRPC。


如需將 gRPC 服務新增至現有 ASP.NET Core 應用程式的指示，請參閱 [將 gRPC 服務新增至 ASP.NET Core 應用程式](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)。

如需建立 gRPC 專案的指示，請參閱 <xref:tutorials/grpc/grpc-start> 。

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web in ASP.NET Core 與 Envoy

有兩個選項可讓您選擇如何將 gRPC 新增至 ASP.NET Core 應用程式：

* 在 ASP.NET Core 中支援 gRPC-Web 與 gRPC HTTP/2。 此選項會使用封裝所提供的中介軟體 `Grpc.AspNetCore.Web` 。
* 使用 [Envoy proxy 的](https://www.envoyproxy.io/) gRPC web 支援將 gRPC 轉譯成 gRPC HTTP/2。 接著會將已轉譯的呼叫轉送至 ASP.NET Core 應用程式。

每種方法都有優缺點。 如果應用程式的環境已經使用 Envoy 作為 proxy，也可以使用 Envoy 來提供 gRPC Web 支援。 針對僅需要 ASP.NET Core 的 gRPC Web 基本解決方案， `Grpc.AspNetCore.Web` 是不錯的選擇。

## <a name="configure-grpc-web-in-aspnet-core"></a>在 ASP.NET Core 中設定 gRPC Web

ASP.NET Core 中裝載的 gRPC 服務可以設定為支援 gRPC Web 和 HTTP/2 gRPC。 gRPC-Web 不需要對服務進行任何變更。 唯一的修改是啟動設定。

若要使用 ASP.NET Core gRPC 服務來啟用 gRPC-Web：

* 將參考新增至 [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 封裝。
* 將應用程式新增至 Startup.cs，以將應用程式設定為使用 gRPC-Web `UseGrpcWeb` `EnableGrpcWeb` ： *Startup.cs*

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

上述程式碼：

* 新增 gRPC-Web 中介軟體、 `UseGrpcWeb` 路由之後和端點之前。
* 指定 `endpoints.MapGrpcService<GreeterService>()` 方法支援 GRPC Web with `EnableGrpcWeb` 。 

或者，您可以設定 gRPC Web 中介軟體，讓所有服務預設都支援 gRPC-Web，而 `EnableGrpcWeb` 不需要。 指定 `new GrpcWebOptions { DefaultEnabled = true }` 加入中介軟體的時間。

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> 有一個已知的問題，會導致 gRPC 在 .NET Core 3.x 中 [由 Http.sys](xref:fundamentals/servers/httpsys) 裝載時失敗。
>
> 您可以從 [這裡](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)取得 Http.sys 的 gRPC Web 工作使用方法。

### <a name="grpc-web-and-cors"></a>gRPC-Web 和 CORS

瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。 這種限制適用于使用瀏覽器應用程式進行 gRPC 對 Web 的呼叫。 例如，由所提供的瀏覽器應用程式被 `https://www.contoso.com` 封鎖，無法呼叫裝載于的 GRPC Web 服務 `https://services.contoso.com` 。 跨原始資源分享 (CORS) 可用於放寬這種限制。

若要允許瀏覽器應用程式進行跨原始 gRPC Web 呼叫，請 [在 ASP.NET Core 中設定 CORS](xref:security/cors)。 使用內建 CORS 支援，並使用來公開 gRPC 特定的標頭 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> 。

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

上述程式碼：

* 呼叫 `AddCors` 以新增 cors 服務，並設定可公開 gRPC 特定標頭的 cors 原則。
* `UseCors`在路由之後和端點之前新增 CORS 中介軟體的呼叫。
* 指定 `endpoints.MapGrpcService<GreeterService>()` 使用支援 CORS 的方法 `RequiresCors` 。

### <a name="grpc-web-and-streaming"></a>gRPC-Web 和串流

傳統的 gRPC over HTTP/2 支援所有方向的串流。 gRPC-Web 提供有限的串流支援：

* gRPC-網頁瀏覽器用戶端不支援呼叫用戶端串流和雙向串流方法。
* ASP.NET Core 裝載于 Azure App Service 上的 gRPC 服務，而且 IIS 不支援雙向串流。

使用 gRPC Web 時，我們只建議使用一元方法和伺服器串流方法。

## <a name="call-grpc-web-from-the-browser"></a>從瀏覽器呼叫 gRPC-Web

瀏覽器應用程式可以使用 gRPC-Web 來呼叫 gRPC services。 從瀏覽器呼叫 gRPC 服務與 gRPC 時，有一些需求和限制：

* 伺服器必須設定為支援 gRPC Web。
* 不支援用戶端串流和雙向串流呼叫。 支援伺服器串流。
* 在不同的網域上呼叫 gRPC 服務需要在伺服器上設定 [CORS](xref:security/cors) 。

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web 用戶端

有一個 JavaScript gRPC Web 用戶端。 如需如何從 JavaScript 使用 gRPC Web 的指示，請參閱 [使用 gRPC 撰寫 javascript 用戶端程式代碼](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>使用 .NET gRPC 用戶端設定 gRPC-Web

您可以設定 .NET gRPC 用戶端來進行 gRPC Web 呼叫。 這適用于裝載 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 于瀏覽器中的應用程式，且具有 JavaScript 程式碼的相同 HTTP 限制。 使用 .NET 用戶端呼叫 gRPC Web 與 [HTTP/2 gRPC 相同](xref:grpc/client)。 唯一的修改是通道的建立方式。

若要使用 gRPC-Web：

* 將參考新增至 [Grpc .net. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 封裝。
* 確定 [Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client) 的參考是2.29.0 或更高。
* 將通道設定為使用 `GrpcWebHandler` ：

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

上述程式碼：

* 設定通道以使用 gRPC Web。
* 建立用戶端，並使用通道進行呼叫。

`GrpcWebHandler` 具有下列設定選項：

* **InnerHandler** ： <xref:System.Net.Http.HttpMessageHandler> 建立 gRPC HTTP 要求的基礎，例如 `HttpClientHandler` 。
* **GrpcWebMode** ：列舉型別，這個型別會指定 gRPC HTTP 要求是否 `Content-Type` 為 `application/grpc-web` 或 `application/grpc-web-text` 。
    * `GrpcWebMode.GrpcWeb` 設定要在不編碼的情況下傳送的內容。 預設值。
    * `GrpcWebMode.GrpcWebText` 將內容設定為 base64 編碼。 在瀏覽器中進行伺服器串流呼叫的必要項。
* **HttpVersion** ： HTTP 通訊協定， `Version` 用來設定基礎 gRPC Http 要求的 [HttpRequestMessage。](xref:System.Net.Http.HttpRequestMessage.Version) gRPC-Web 不需要特定版本，除非已指定，否則不會覆寫預設值。

> [!IMPORTANT]
> 產生的 gRPC 用戶端具有可呼叫一元方法的同步和非同步方法。 例如， `SayHello` 為 sync 且 `SayHelloAsync` 為 async。 在應用程式中呼叫同步方法 Blazor WebAssembly 會導致應用程式沒有回應。 非同步方法必須一律用於中 Blazor WebAssembly 。

### <a name="use-grpc-client-factory-with-grpc-web"></a>使用 gRPC client factory 搭配 gRPC-Web

您可以使用 gRPC 與 [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory)的整合來建立 gRPC Web 相容的 .net 用戶端。

若要使用 gRPC-Web 搭配用戶端 factory：

* 將套件參考新增至下列封裝的專案檔：
  * [Grpc .Net](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [Grpc .Net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* 使用泛型擴充方法 (DI) 註冊相依性插入的 gRPC 用戶端 `AddGrpcClient` 。 在 Blazor WebAssembly 應用程式中，服務是在中向 DI 註冊的 `Program.cs` 。
* `GrpcWebHandler`使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 擴充方法進行設定。

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

如需詳細資訊，請參閱<xref:grpc/clientfactory>。

## <a name="additional-resources"></a>其他資源

* [適用于 Web 用戶端 GitHub 專案的 gRPC](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
