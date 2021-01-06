---
title: ASP.NET Core 中的要求功能
author: ardalis
description: 了解有關 HTTP 要求和回應的網頁伺服器實作詳細資料，其定義於 ASP.NET Core 的介面中。
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "95870421"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core 中的要求功能

作者：[Steve Smith](https://ardalis.com/)

`HttpContext`應用程式和中介軟體用來處理要求的 API，在其下稱為 *功能介面* 的抽象層。 每個功能介面都會提供所公開之功能的詳細子集 `HttpContext` 。 這些介面可以加入、修改、包裝、取代，或甚至由伺服器或中介軟體移除，因為要求會經過處理，而不需要重新執行整個 `HttpContext` 。 它們也可以用來模擬測試時的功能。

## <a name="feature-collections"></a>功能集合

的 <xref:Microsoft.AspNetCore.Http.HttpContext.Features> 屬性 `HttpContext` 提供目前要求之功能介面集合的存取權。 由於功能集合即使在要求內容中都是可變動的，因此可以使用中介軟體來修改該集合，並新增其他功能的支援。 某些 advanced 功能只能透過功能集合存取相關聯的介面來使用。

## <a name="feature-interfaces"></a>功能介面

ASP.NET Core 定義中的一些常見 HTTP 功能介面 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> ，這些介面會由各種伺服器和中介軟體共用，以識別它們所支援的功能。 伺服器和中介軟體也可以提供自己的介面，以提供額外的功能。

大部分的功能介面都會提供選擇性的亮顯功能，而其相關聯 `HttpContext` 的 api 會在功能不存在時提供預設值。 下列內容會視需要指定一些介面，因為它們會提供核心要求和回應功能，而且必須執行才能處理要求。

下列功能介面來自 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> ：

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>：定義 HTTP 要求的結構，包括通訊協定、路徑、查詢字串、標頭和主體。 需要此功能才能處理要求。

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>：定義 HTTP 回應的結構，包括狀態碼、標頭和回應主體。 需要此功能才能處理要求。

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>：定義使用、或檔案寫出回應主體的不同方式 `Stream` `PipeWriter` 。 需要此功能才能處理要求。 這會取代 `IHttpResponseFeature.Body` 和 `IHttpSendFileFeature` 。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>：保留 <xref:System.Security.Claims.ClaimsPrincipal> 目前與要求相關聯的。

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>：用來剖析和快取傳入的 HTTP 和多部分表單提交。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>：用來控制要求或回應主體是否允許同步 IO 作業。

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>：定義停用要求和/或回應之緩衝的方法。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>：定義連接識別碼和本機和遠端位址和埠的屬性。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：控制目前要求所允許的要求主體大小上限。

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`：指出要求是否可以有主體。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>：加入可實作為唯一識別要求的屬性。

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>：定義中止連接或偵測要求是否已提前終止的支援，例如用戶端中斷連線。

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>：提供要求尾端標頭（如果有）的存取權。

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>：用來傳送重設訊息給支援它們的通訊協定，例如 HTTP/2 或 HTTP/3。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>：可讓應用程式在支援時提供回應尾端標頭。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>：定義以非同步方式傳送檔案的方法。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>：定義 [HTTP 升級](https://tools.ietf.org/html/rfc2616.html#section-14.42)的支援，可讓用戶端指定要在伺服器希望切換通訊協定時使用的其他通訊協定。

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>：定義支援 web 通訊端的 API。

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>：控制是否應透過 HTTPS 連接使用回應壓縮。

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>：儲存 <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> 每個要求應用程式狀態的集合。

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>：剖析和快取查詢字串。
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>：以形式表示要求主體 <xref:System.IO.Pipelines.PipeReader> 。
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>：剖析和快取要求 `Cookie` 標頭值。

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>：控制如何將回應套用 cookie 至 `Set-Cookie` 標頭。

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>：這項功能可讓您存取要求伺服器變數，例如 IIS 所提供的變數。

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>：提供 <xref:System.IServiceProvider> 具有範圍要求服務的存取權。

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>：定義 `ISessionFactory` <xref:Microsoft.AspNetCore.Http.ISession> 支援使用者會話的和抽象概念。 `ISessionFeature` 是由 (所執行， <xref:Microsoft.AspNetCore.Session.SessionMiddleware> 請參閱 <xref:fundamentals/app-state>) 。

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>：定義用於抓取用戶端憑證的 API。

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>：定義使用 TLS 權杖系結參數的方法。
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>：用來查詢、授與及撤銷使用者關於與網站活動和功能相關之使用者資訊儲存的同意。
   
::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
