---
title: 將 HTTP 處理常式和模組遷移至 ASP.NET Core 中介軟體
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
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
uid: migration/http-modules
ms.openlocfilehash: 8be09171991964540cd41a1324fb87503591151f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632170"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>將 HTTP 處理常式和模組遷移至 ASP.NET Core 中介軟體

本文說明如何將現有 [的 ASP.NET HTTP 模組和處理常式從 system.webserver](/iis/configuration/system.webserver/) 遷移至 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)。

## <a name="modules-and-handlers-revisited"></a>進行的模組和處理常式

在繼續 ASP.NET Core 中介軟體之前，讓我們先回顧一下 HTTP 模組和處理常式的運作方式：

![模組處理常式](http-modules/_static/moduleshandlers.png)

**處理常式如下：**

* 執行[IHttpHandler](/dotnet/api/system.web.ihttphandler)的類別

* 用來處理具有指定之檔案名或副檔名的要求，例如 *. report*

* 在*Web.config*中[設定](/iis/configuration/system.webserver/handlers/)

**模組包括：**

* 執行[IHttpModule](/dotnet/api/system.web.ihttpmodule)的類別

* 針對每個要求叫用

* 能夠進行短暫的 (停止進一步處理要求) 

* 可以新增至 HTTP 回應，或建立自己的

* 在*Web.config*中[設定](/iis/configuration/system.webserver/modules/)

**模組處理傳入要求的順序取決於：**

1. [應用程式生命週期](https://msdn.microsoft.com/library/ms227673.aspx)，這是 ASP.NET： [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)、 [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)等所引發的一連串事件。每個模組都可以建立一個或多個事件的處理常式。

2. 針對相同事件， *Web.config*中設定它們的順序。

除了模組之外，您還可以將生命週期事件的處理常式加入至 *Global.asax.cs* 檔案。 這些處理常式會在設定的模組中的處理常式之後執行。

## <a name="from-handlers-and-modules-to-middleware"></a>從處理常式和模組到中介軟體

**中介軟體比 HTTP 模組和處理常式簡單：**

* 模組、處理常式、 *Global.asax.cs*、 *Web.config* (除了 IIS 設定) 和應用程式生命週期消失之外

* 模組和處理常式的角色已由中介軟體接管

* 中介軟體是使用程式碼來設定，而不是在 *Web.config*

::: moniker range=">= aspnetcore-3.0"

* [管線分支](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 可讓您根據 URL，以及要求標頭、查詢字串等，將要求傳送至特定中介軟體。

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* [管線分支](xref:fundamentals/middleware/index#use-run-and-map) 可讓您根據 URL，以及要求標頭、查詢字串等，將要求傳送至特定中介軟體。

::: moniker-end

**中介軟體與模組非常類似：**

* 針對每個要求以準則叫用

* 藉由[不將要求傳遞至下一個中介軟體](#http-modules-shortcircuiting-middleware)，而能夠將要求短路

* 能夠建立自己的 HTTP 回應

**中介軟體和模組會以不同的連續處理：**

* 中介軟體的順序是根據它們插入要求管線的順序，而模組的順序主要是根據 [應用程式生命週期](https://msdn.microsoft.com/library/ms227673.aspx) 事件

* 回應的中介軟體順序與要求的順序相反，因為要求和回應的模組順序相同

* 請參閱 [使用 IApplicationBuilder 建立中介軟體管線](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![中介軟體](http-modules/_static/middleware.png)

請注意，在上圖中，驗證中介軟體會簡短縮短要求。

## <a name="migrating-module-code-to-middleware"></a>將模組程式碼遷移至中介軟體

現有的 HTTP 模組看起來會像這樣：

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

如 [中介軟體](xref:fundamentals/middleware/index) 頁面所示，ASP.NET Core 中介軟體是一種類別，可公開 `Invoke` 接受 `HttpContext` 並傳回的方法 `Task` 。 新的中介軟體看起來像這樣：

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

上述中介軟體範本取自 [撰寫中介軟體](xref:fundamentals/middleware/write)的章節。

*MyMiddlewareExtensions* helper 類別可讓您更輕鬆地在類別中設定中介軟體 `Startup` 。 `UseMyMiddleware`方法會將中介軟體類別新增至要求管線。 中介軟體所需的服務會插入中介軟體的函式中。

<a name="http-modules-shortcircuiting-middleware"></a>

您的模組可能會終止要求，例如，如果使用者未獲授權：

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

中介軟體不會 `Invoke` 在管線中的下一個中介軟體上呼叫，藉此處理此情況。 請記住，這不會完全終止要求，因為當回應透過管線回傳時，仍會叫用先前的中介軟體。

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

當您將模組的功能遷移至新的中介軟體時，可能會發現您的程式碼未進行編譯，因為 `HttpContext` ASP.NET Core 中的類別已大幅變更。 [稍後](#migrating-to-the-new-httpcontext)，您將瞭解如何遷移至新的 ASP.NET Core HttpCoNtext。

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>將模組插入遷移至要求管線

HTTP 模組通常會使用 *Web.config*新增至要求管線：

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

將 [新的中介軟體新增](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 至類別中的要求管線，以進行轉換 `Startup` ：

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

在管線中，您插入新中介軟體的確切位置，取決於它以模組形式處理的事件 (`BeginRequest` 、等等 `EndRequest` ) 及其在 *Web.config*的模組清單中的順序。

如先前所述，ASP.NET Core 中沒有應用程式生命週期，中介軟體的處理回應順序與模組所使用的順序不同。 這可能會讓您的訂購決策更具挑戰性。

如果排序變成問題，您可以將模組分割成多個中介軟體元件，以便個別進行排序。

## <a name="migrating-handler-code-to-middleware"></a>將處理常式程式碼遷移至中介軟體

HTTP 處理常式看起來像這樣：

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

在您的 ASP.NET Core 專案中，您會將其轉譯為類似如下的中介軟體：

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

此中介軟體非常類似于模組的對應中介軟體。 唯一的差別在於這裡沒有任何呼叫 `_next.Invoke(context)` 。 這是合理的，因為處理常式是在要求管線的結尾，所以不會有下一個中介軟體可叫用。

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>將處理常式插入遷移至要求管線

設定 HTTP 處理常式是在 *Web.config* 中完成，並看起來像這樣：

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

您可以藉由將新的處理常式中介軟體新增至類別中的要求管線來轉換 `Startup` ，類似于從模組轉換的中介軟體。 這種方法的問題在於它會將所有要求傳送至新的處理常式中介軟體。 不過，您只需要具有指定延伸模組的要求才能到達您的中介軟體。 這會提供您與 HTTP 處理常式相同的功能。

其中一個解決方法是使用擴充方法，將管線分支給具有指定擴充的要求 `MapWhen` 。 您可以在 `Configure` 新增其他中介軟體的相同方法中執行此動作：

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` 採用下列參數：

1. 使用的 lambda， `HttpContext` `true` 如果要求應該在分支下移，則會傳回。 這表示您可以不只根據其擴充功能來分支要求，也可以在要求標頭、查詢字串參數等上進行。

2. 接受 `IApplicationBuilder` 並加入分支所有中介軟體的 lambda。 這表示您可以在處理常式中介軟體之前，將其他中介軟體新增至分支。

將在所有要求上叫用分支之前新增至管線的中介軟體;分支不會有任何影響。

## <a name="loading-middleware-options-using-the-options-pattern"></a>使用選項模式載入中介軟體選項

某些模組和處理常式具有儲存在 *Web.config*中的設定選項。不過，在 ASP.NET Core 新的設定模型是用來取代 *Web.config*。

新的設定 [系統](xref:fundamentals/configuration/index) 提供下列選項來解決此問題：

* 將選項直接插入中介軟體中，如下一 [節](#loading-middleware-options-through-direct-injection)所示。

* 使用 [選項模式](xref:fundamentals/configuration/options)：

1. 建立類別來保存中介軟體選項，例如：

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. 儲存選項值

   設定系統可讓您將選項值儲存在任何您想要的位置。 不過，大部分的網站會使用 *appsettings.js*，因此我們將採用該方法：

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   這裡的*MyMiddlewareOptionsSection*是區段名稱。 它不一定要與選項類別的名稱相同。

3. 建立選項值與 options 類別的關聯

    選項模式使用 ASP.NET Core 的相依性插入架構，將選項類型 (例如 `MyMiddlewareOptions`) 與 `MyMiddlewareOptions` 具有實際選項的物件產生關聯。

    更新您的 `Startup` 類別：

   1. 如果您使用 *appsettings.json*，請將它新增至函式中的設定產生器 `Startup` ：

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. 設定選項服務：

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. 將您的選項與您的 options 類別產生關聯：

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. 將選項插入中介軟體函式中。 這類似于在控制器中插入選項。

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   將中介軟體新增至的 [UseMiddleware](#http-modules-usemiddleware) 擴充方法 `IApplicationBuilder` 會負責相依性插入。

   這並不限於 `IOptions` 物件。 中介軟體所需的任何其他物件都可以透過這種方式插入。

## <a name="loading-middleware-options-through-direct-injection"></a>透過直接插入載入中介軟體選項

選項模式的優點是它會在選項值和取用者之間建立鬆散結合。 當您將 options 類別與實際的選項值建立關聯之後，任何其他類別都可以透過相依性插入架構取得選項的存取權。 不需要傳遞選項值。

但是，如果您想要使用相同的中介軟體兩次，並使用不同的選項，這會中斷。 例如，在不同分支中使用的授權中介軟體允許不同的角色。 您無法將兩個不同的選項物件與一個 options 類別建立關聯。

解決方法是在類別中取得具有實際選項值的選項物件 `Startup` ，並將它們直接傳遞至中介軟體的每個實例。

1. 新增第二個金鑰以 *appsettings.js于*

   若要將第二組選項新增至 *appsettings.json* 檔案，請使用新的金鑰來唯一識別它：

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. 取出選項值，並將其傳遞至中介軟體。 `Use...`擴充方法 (會將中介軟體新增至管線) 是要傳入選項值的邏輯位置： 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. 啟用中介軟體以採用 options 參數。 提供擴充方法的多載 `Use...` ， (會採用 options 參數，並將其傳遞給 `UseMiddleware`) 。 `UseMiddleware`使用參數呼叫時，會在將中介軟體物件具現化時，將參數傳遞至中介軟體函式。

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   請注意，這會如何包裝物件中的選項物件 `OptionsWrapper` 。 這會依照 `IOptions` 中介軟體函式的預期執行。

## <a name="migrating-to-the-new-httpcontext"></a>遷移至新的 HttpCoNtext

您稍早看到 `Invoke` 中介軟體中的方法接受下列類型的參數 `HttpContext` ：

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` 在 ASP.NET Core 中已大幅變更。 本節說明如何將 system.servicemodel 最常 [使用的屬性轉譯為新](/dotnet/api/system.web.httpcontext) 的 `Microsoft.AspNetCore.Http.HttpContext` 。

### <a name="httpcontext"></a>HttpcoNtext.current

**HttpCoNtext** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**唯一的要求識別碼 (沒有 system.string 對應) **

為您提供每個要求的唯一識別碼。 包含在您的記錄中非常有用。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpCoNtext. 要求

**HttpMethod** 會將轉換成：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpcoNtext.current** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpcoNtext. Url** 和 **HttpCoNtext. RawUrl** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**IsSecureConnection** 會將轉換成：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**UserHostAddress** 會將轉換成：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpCoNtext. 要求。 Cookie可轉換為** ：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**RequestCoNtext. RouteData** 可轉換為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpCoNtext. 標頭** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**UserAgent** 會將轉換成：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**UrlReferrer** 會將轉換成：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpCoNtext** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpCoNtext. Form** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> 只有在內容子類型為 *x-www 表單 urlencoded* 或 *表單資料*時，才會讀取表單值。

**InputStream** 會將轉換成：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> 此程式碼只能在管線結尾的處理常式類型中介軟體中使用。
>
>您可以針對每個要求唯讀取一次以上所示的原始主體。 在第一次讀取之後嘗試讀取主體的中介軟體將會讀取空白主體。
>
>這並不適用于讀取如先前所示的表單，因為這是從緩衝區完成的。

### <a name="httpcontextresponse"></a>HttpCoNtext. 回應

**HttpcoNtext. response** . **StatusDescription** 可轉換為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**ContentEncoding** 和 **HTTPcoNtext** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpCoNtext** 本身的 ContentType 也會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpCoNtext。輸出** 會轉譯為：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpCoNtext. TransmitFile**

[此處](../fundamentals/request-features.md#middleware-and-request-features)會討論如何提供檔案。

**HttpCoNtext. 標頭**

傳送回應標頭很複雜，因為如果您在將任何專案寫入回應本文之後設定這些標頭，就不會傳送這些標頭。

解決方法是設定回呼方法，這個方法會在寫入回應開始之前呼叫。 在中介軟體的方法開始時，最好這麼做 `Invoke` 。 這是設定回應標頭的回呼方法。

下列程式碼會設定名為的回呼方法 `SetHeaders` ：

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders`回呼方法看起來像這樣：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpCoNtext. 回應。 Cookie！**

Cookie在*設定 Cookie *回應標頭中移動至瀏覽器。 如此一來，傳送 cookie 必須與用來傳送回應標頭的回呼相同：

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies`回呼方法如下所示：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>其他資源

* [HTTP 處理常式和 HTTP 模組總覽](/iis/configuration/system.webserver/)
* [組態](xref:fundamentals/configuration/index)
* [應用程式啟動](xref:fundamentals/startup)
* [中介軟體](xref:fundamentals/middleware/index)
