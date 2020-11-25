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
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870421"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="cde49-103">ASP.NET Core 中的要求功能</span><span class="sxs-lookup"><span data-stu-id="cde49-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="cde49-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cde49-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cde49-105">`HttpContext`應用程式和中介軟體用來處理要求的 API，在其下稱為 *功能介面* 的抽象層。</span><span class="sxs-lookup"><span data-stu-id="cde49-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer underneath it called *feature interfaces*.</span></span> <span data-ttu-id="cde49-106">每個功能介面都會提供所公開之功能的詳細子集 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="cde49-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="cde49-107">這些介面可以加入、修改、包裝、取代，或甚至由伺服器或中介軟體移除，因為要求會經過處理，而不需要重新執行整個 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="cde49-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="cde49-108">它們也可以用來模擬測試時的功能。</span><span class="sxs-lookup"><span data-stu-id="cde49-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="cde49-109">功能集合</span><span class="sxs-lookup"><span data-stu-id="cde49-109">Feature collections</span></span>

<span data-ttu-id="cde49-110">的 <xref:Microsoft.AspNetCore.Http.HttpContext.Features> 屬性 `HttpContext` 提供目前要求之功能介面集合的存取權。</span><span class="sxs-lookup"><span data-stu-id="cde49-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="cde49-111">由於功能集合即使在要求內容中都是可變動的，因此可以使用中介軟體來修改該集合，並新增其他功能的支援。</span><span class="sxs-lookup"><span data-stu-id="cde49-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="cde49-112">某些 advanced 功能只能透過功能集合存取相關聯的介面來使用。</span><span class="sxs-lookup"><span data-stu-id="cde49-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="cde49-113">功能介面</span><span class="sxs-lookup"><span data-stu-id="cde49-113">Feature interfaces</span></span>

<span data-ttu-id="cde49-114">ASP.NET Core 定義中的一些常見 HTTP 功能介面 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> ，這些介面會由各種伺服器和中介軟體共用，以識別它們所支援的功能。</span><span class="sxs-lookup"><span data-stu-id="cde49-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="cde49-115">伺服器和中介軟體也可以提供自己的介面，以提供額外的功能。</span><span class="sxs-lookup"><span data-stu-id="cde49-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="cde49-116">大部分的功能介面都會提供選擇性的亮顯功能，而其相關聯 `HttpContext` 的 api 會在功能不存在時提供預設值。</span><span class="sxs-lookup"><span data-stu-id="cde49-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="cde49-117">下列內容會視需要指定一些介面，因為它們會提供核心要求和回應功能，而且必須執行才能處理要求。</span><span class="sxs-lookup"><span data-stu-id="cde49-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="cde49-118">下列功能介面來自 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> ：</span><span class="sxs-lookup"><span data-stu-id="cde49-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="cde49-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>：定義 HTTP 要求的結構，包括通訊協定、路徑、查詢字串、標頭和主體。</span><span class="sxs-lookup"><span data-stu-id="cde49-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="cde49-120">需要此功能才能處理要求。</span><span class="sxs-lookup"><span data-stu-id="cde49-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="cde49-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>：定義 HTTP 回應的結構，包括狀態碼、標頭和回應主體。</span><span class="sxs-lookup"><span data-stu-id="cde49-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="cde49-122">需要此功能才能處理要求。</span><span class="sxs-lookup"><span data-stu-id="cde49-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cde49-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>：定義使用、或檔案寫出回應主體的不同方式 `Stream` `PipeWriter` 。</span><span class="sxs-lookup"><span data-stu-id="cde49-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="cde49-124">需要此功能才能處理要求。</span><span class="sxs-lookup"><span data-stu-id="cde49-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="cde49-125">這會取代 `IHttpResponseFeature.Body` 和 `IHttpSendFileFeature` 。</span><span class="sxs-lookup"><span data-stu-id="cde49-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="cde49-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>：保留 <xref:System.Security.Claims.ClaimsPrincipal> 目前與要求相關聯的。</span><span class="sxs-lookup"><span data-stu-id="cde49-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="cde49-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>：用來剖析和快取傳入的 HTTP 和多部分表單提交。</span><span class="sxs-lookup"><span data-stu-id="cde49-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cde49-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>：用來控制要求或回應主體是否允許同步 IO 作業。</span><span class="sxs-lookup"><span data-stu-id="cde49-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cde49-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>：定義停用要求和/或回應之緩衝的方法。</span><span class="sxs-lookup"><span data-stu-id="cde49-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="cde49-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>：定義連接識別碼和本機和遠端位址和埠的屬性。</span><span class="sxs-lookup"><span data-stu-id="cde49-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cde49-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：控制目前要求所允許的要求主體大小上限。</span><span class="sxs-lookup"><span data-stu-id="cde49-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cde49-132">`IHttpRequestBodyDetectionFeature`：指出要求是否可以有主體。</span><span class="sxs-lookup"><span data-stu-id="cde49-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="cde49-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>：加入可實作為唯一識別要求的屬性。</span><span class="sxs-lookup"><span data-stu-id="cde49-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="cde49-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>：定義中止連接或偵測要求是否已提前終止的支援，例如用戶端中斷連線。</span><span class="sxs-lookup"><span data-stu-id="cde49-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cde49-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>：提供要求尾端標頭（如果有）的存取權。</span><span class="sxs-lookup"><span data-stu-id="cde49-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="cde49-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>：用來傳送重設訊息給支援它們的通訊協定，例如 HTTP/2 或 HTTP/3。</span><span class="sxs-lookup"><span data-stu-id="cde49-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="cde49-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>：可讓應用程式在支援時提供回應尾端標頭。</span><span class="sxs-lookup"><span data-stu-id="cde49-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cde49-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>：定義以非同步方式傳送檔案的方法。</span><span class="sxs-lookup"><span data-stu-id="cde49-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="cde49-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>：定義 [HTTP 升級](https://tools.ietf.org/html/rfc2616.html#section-14.42)的支援，可讓用戶端指定要在伺服器希望切換通訊協定時使用的其他通訊協定。</span><span class="sxs-lookup"><span data-stu-id="cde49-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="cde49-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>：定義支援 web 通訊端的 API。</span><span class="sxs-lookup"><span data-stu-id="cde49-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cde49-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>：控制是否應透過 HTTPS 連接使用回應壓縮。</span><span class="sxs-lookup"><span data-stu-id="cde49-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="cde49-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>：儲存 <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> 每個要求應用程式狀態的集合。</span><span class="sxs-lookup"><span data-stu-id="cde49-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="cde49-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>：剖析和快取查詢字串。</span><span class="sxs-lookup"><span data-stu-id="cde49-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cde49-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>：以形式表示要求主體 <xref:System.IO.Pipelines.PipeReader> 。</span><span class="sxs-lookup"><span data-stu-id="cde49-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="cde49-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>：剖析和快取要求 `Cookie` 標頭值。</span><span class="sxs-lookup"><span data-stu-id="cde49-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="cde49-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>：控制如何將回應套用 cookie 至 `Set-Cookie` 標頭。</span><span class="sxs-lookup"><span data-stu-id="cde49-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="cde49-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>：這項功能可讓您存取要求伺服器變數，例如 IIS 所提供的變數。</span><span class="sxs-lookup"><span data-stu-id="cde49-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="cde49-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>：提供 <xref:System.IServiceProvider> 具有範圍要求服務的存取權。</span><span class="sxs-lookup"><span data-stu-id="cde49-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="cde49-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>：定義 `ISessionFactory` <xref:Microsoft.AspNetCore.Http.ISession> 支援使用者會話的和抽象概念。</span><span class="sxs-lookup"><span data-stu-id="cde49-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="cde49-150">`ISessionFeature` 是由 (所執行， <xref:Microsoft.AspNetCore.Session.SessionMiddleware> 請參閱 <xref:fundamentals/app-state>) 。</span><span class="sxs-lookup"><span data-stu-id="cde49-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="cde49-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>：定義用於抓取用戶端憑證的 API。</span><span class="sxs-lookup"><span data-stu-id="cde49-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="cde49-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>：定義使用 TLS 權杖系結參數的方法。</span><span class="sxs-lookup"><span data-stu-id="cde49-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="cde49-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>：用來查詢、授與及撤銷使用者關於與網站活動和功能相關之使用者資訊儲存的同意。</span><span class="sxs-lookup"><span data-stu-id="cde49-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cde49-154">其他資源</span><span class="sxs-lookup"><span data-stu-id="cde49-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
