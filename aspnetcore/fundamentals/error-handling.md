---
title: 處理 ASP.NET Core 中的錯誤
author: rick-anderson
description: 了解如何處理 ASP.NET Core 應用程式中的錯誤。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751687"
---
# <a name="handle-errors-in-aspnet-core"></a>處理 ASP.NET Core 中的錯誤

::: moniker range=">= aspnetcore-5.0"

[Kirk Larkin](https://twitter.com/serpent5)、 [Tom Dykstra](https://github.com/tdykstra/)和[Steve Smith](https://ardalis.com/)

本文涵蓋 ASP.NET Core web 應用程式中處理錯誤的常見方法。 請參閱 <xref:web-api/handle-errors> ，以取得 Web api。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。  ([下載方式](xref:index#how-to-download-a-sample)。在測試範例應用程式時，) F12 瀏覽器開發人員工具上的 [網路] 索引標籤很有用。

## <a name="developer-exception-page"></a>開發人員例外狀況頁面

「開發人員例外狀況頁面」會顯示要求例外狀況的詳細資訊。 ASP.NET Core 範本會產生下列程式碼：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

上述反白顯示的程式碼可在 [開發環境](xref:fundamentals/environments)中執行應用程式時，啟用開發人員例外狀況頁面。

範本 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 會提早放置於中介軟體管線中，讓它可以攔截接下來中介軟體中擲回的例外狀況。

上述程式碼只會在應用程式于開發環境中執行時，**才** 啟用開發人員例外狀況頁面 *。 當應用程式在生產環境中執行時，不應公開顯示詳細的例外狀況資訊。 如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。

開發人員例外狀況頁面包含下列有關例外狀況和要求的資訊：

_ 堆疊追蹤
* 查詢字串參數（如果有的話）
* Cookie如果有的話
* 標題

## <a name="exception-handler-page"></a>例外處理常式頁面

若要針對 [生產環境](xref:fundamentals/environments)設定自訂錯誤處理頁面，請呼叫 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 。 此例外狀況處理中介軟體：

* 攔截並記錄例外狀況。
* 使用指定的路徑，在替代管線中重新執行要求。 如果回應已啟動，就不會重新執行要求。 範本產生的程式碼會使用路徑重新執行要求 `/Error` 。

在下列範例中，會 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非開發環境中加入例外狀況處理中介軟體：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorPages 應用程式範本會 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> `ErrorModel` 在 [ *pages* ] 資料夾中提供錯誤頁面 () 和類別 () 。 在 MVC 應用程式中，專案範本包含 `Error` 動作方法和首頁控制器的錯誤視圖。

例外狀況處理中介軟體會使用 *原始* HTTP 方法重新執行要求。 如果錯誤處理常式端點受限於一組特定的 HTTP 方法，則只會針對這些 HTTP 方法執行。 例如，使用屬性的 MVC 控制器動作只會 `[HttpGet]` 執行 GET 要求。 為了確保 *所有要求都* 到達自訂錯誤處理頁面，請不要將它們限制為一組特定的 HTTP 方法。

若要根據原始的 HTTP 方法，以不同的方式處理例外狀況：

* 若為 Razor 頁面，請建立多個處理常式方法。 例如，使用 `OnGet` 來處理 GET 例外狀況，並使用 `OnPost` 來處理 POST 例外狀況。
* 針對 MVC，將 HTTP 動詞屬性套用至多個動作。 例如，使用 `[HttpGet]` 來處理 GET 例外狀況，並使用 `[HttpPost]` 來處理 POST 例外狀況。

若要允許未經驗證的使用者查看自訂錯誤處理頁面，請確定它支援匿名存取。

### <a name="access-the-exception"></a>存取例外狀況

用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 來存取例外狀況和錯誤處理常式中的原始要求路徑。 下列程式碼會加入 `ExceptionMessage` ASP.NET Core 範本所產生的預設 *Pages/Error. .cs* ：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> 請 **勿** 提供敏感性的錯誤資訊給用戶端。 提供錯誤有安全性風險。

若要測試 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的例外狀況：

* 將環境設定為生產環境。
* 從 `webBuilder.UseStartup<Startup>();` *Program.cs* 中移除批註。
* 在首頁上選取 [ **觸發例外** 狀況]。

## <a name="exception-handler-lambda"></a>例外處理常式 Lambda

[自訂例外處理常式頁面](#exception-handler-page)的替代方法，便是將 Lambda 提供給 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。 使用 lambda 可讓您在傳回回應之前存取錯誤。

下列程式碼會使用 lambda 來處理例外狀況：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> 請 **勿** 從 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 提供錯誤資訊給用戶端。 提供錯誤有安全性風險。

測試 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的例外狀況處理 lambda：

* 將環境設定為生產環境。
* 從 `webBuilder.UseStartup<StartupLambda>();` *Program.cs* 中移除批註。
* 在首頁上選取 [ **觸發例外** 狀況]。

## <a name="usestatuscodepages"></a>UseStatusCodePages

根據預設，ASP.NET Core 的應用程式不會提供 HTTP 錯誤狀態碼（例如 *404-找不* 到）的狀態字碼頁面。 當應用程式遇到沒有主體的 HTTP 400-599 錯誤狀態碼時，會傳回狀態碼和空白的回應主體。 若要提供狀態字碼頁面，請使用狀態字碼頁面中介軟體。 若要針對常見的錯誤狀態碼啟用預設的純文字處理常式，請呼叫 `Startup.Configure` 方法中的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

`UseStatusCodePages`要求處理中介軟體之前呼叫。 例如，在 `UseStatusCodePages` 靜態檔案中介軟體和端點中介軟體之前呼叫。

`UseStatusCodePages`未使用時，流覽至不含端點的 URL 會傳回瀏覽器相依的錯誤訊息，指出找不到端點。 例如，流覽至 `Home/Privacy2` 。 當 `UseStatusCodePages` 呼叫時，瀏覽器會傳回：

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` 通常不會在生產環境中使用，因為它會傳回不適合用戶的訊息。

若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試：

* 將環境設定為生產環境。
* 從 `webBuilder.UseStartup<StartupUseStatusCodePages>();` *Program.cs* 中移除批註。
* 選取首頁首頁上的連結。

> [!NOTE]
> 狀態字碼頁面中介軟體 **不** 會攔截例外狀況。 若要提供自訂錯誤處理頁面，請使用 [ [例外狀況處理常式] 頁面](#exception-handler-page)。

### <a name="usestatuscodepages-with-format-string"></a>具格式字串的 UseStatusCodePages

若要自訂回應內容類型和文字，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用內容類型和格式字串：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

在上述程式碼中， `{0}` 是錯誤碼的預留位置。

`UseStatusCodePages` 使用格式字串時，通常不會在生產環境中使用，因為它會傳回不適合用戶的訊息。

若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupFormat>();` 從 *Program.cs* 中移除批註。

### <a name="usestatuscodepages-with-lambda"></a>具 Lambda 的 UseStatusCodePages

若要指定自訂錯誤處理和回應撰寫程式碼，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用 Lambda 運算式：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` 使用 lambda 時，通常不會在生產環境中使用，因為它會傳回不適合用戶的訊息。

若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupStatusLambda>();` 從 *Program.cs* 中移除批註。

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 擴充方法：

* 傳送「302 - 已找到」[](https://developer.mozilla.org/docs/Web/HTTP/Status/302)狀態碼傳送給用戶端。
* 將用戶端重新導向至 URL 範本中提供的錯誤處理端點。 錯誤處理端點通常會顯示錯誤資訊，並傳回 HTTP 200。

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

URL 範本可以包含 `{0}` 狀態碼的預留位置，如上述程式碼所示。 如果 URL 範本的開頭是 (波狀符號 `~`) ， `~` 會由應用程式取代 `PathBase` 。 在應用程式中指定端點時，請建立端點的 MVC 視圖或 Razor 頁面。 如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的[pages/MyStatusCode。](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)

此方法通常是在下列應用程式相關情況下使用：

* 應用程式應將用戶端重新導向至不同的端點時 (通常是在由其他應用程式處理錯誤的情況下)。 針對 Web 應用程式，用戶端的瀏覽器網址列會反映重新導向後的端點。
* 應用程式不應該保留並傳回原始狀態碼與初始重新導向回應時。

若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupSCredirect>();` 從 *Program.cs* 中移除批註。

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 擴充方法：

* 傳回原始狀態碼給用戶端。
* 可透過使用替代路徑重新執行要求管線來產生回應本文。

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

如果已指定應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。 請確定先放置，才能將 `UseStatusCodePagesWithReExecute` `UseRouting` 要求重新路由至狀態頁面。 如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的[pages/MyStatusCode2。](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)

此方法通常是在下列應用程式相關情況下使用：

* 在不重新導向至其他端點的情況下處理要求。 針對 Web 應用程式，用戶端的瀏覽器網址列會反映原始要求的端點。
* 保留並傳回原始狀態碼與回應時。

URL 和查詢字串範本可能包含 `{0}` 狀態碼的預留位置。 URL 範本的開頭必須是 `/` 。

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

處理錯誤的端點可以取得產生該錯誤的原始 URL，如下列範例所示：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的[pages/MyStatusCode2。](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)

若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupSCreX>();` 從 *Program.cs* 中移除批註。

## <a name="disable-status-code-pages"></a>停用狀態碼頁面

若要停用 MVC 控制器或動作方法的狀態碼頁面，請使用 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 屬性。

若要在 Razor 頁面處理常式方法或 MVC 控制器中停用特定要求的狀態字碼頁面，請使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> ：

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>例外狀況處理程式碼

例外狀況處理頁面中的程式碼也可能會擲回例外狀況。 生產錯誤頁面應該經過徹底的測試，並特別小心避免擲回例外狀況。
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>回應標頭

一旦傳送回應的標頭之後：

* 應用程式就無法變更回應的狀態碼。
* 無法執行任何例外狀況頁面或處理常式。 回應必須完成，否則會中止連線。

## <a name="server-exception-handling"></a>伺服器例外狀況處理

除了應用程式中的例外狀況處理邏輯之外， [HTTP 伺服器的執行](xref:fundamentals/servers/index) 也可以處理一些例外狀況。 如果伺服器在回應標頭傳送之前攔截到例外狀況，伺服器會傳送 `500 - Internal Server Error` 回應，而不會有回應主體。 如果伺服器在回應標頭傳送之後攔截到例外狀況，伺服器會關閉連線。 應用程式未處理的要求會由伺服器處理。 當伺服器處理要求時，任何發生的例外狀況均由伺服器的例外狀況處理功能來處理。 應用程式的自訂錯誤頁面、例外狀況處理中介軟體或篩選條件並不會影響此行為。

## <a name="startup-exception-handling"></a>啟動例外狀況處理

只有裝載層可以處理應用程式啟動期間發生的例外狀況。 可以將主機設定為會[擷取啟動錯誤](xref:fundamentals/host/web-host#capture-startup-errors)和[擷取詳細錯誤](xref:fundamentals/host/web-host#detailed-errors)。

只有在錯誤是於主機位址/連接埠繫結之後發生的情況下，裝載層才能顯示已擷取之啟動錯誤的錯誤頁面。 如果繫結失敗：

* 裝載層會記錄重大例外狀況。
* Dotnet 會處理損毀狀況。
* 當 HTTP 伺服器是 [Kestrel](xref:fundamentals/servers/kestrel) 時，不會顯示任何錯誤頁面。

在 [IIS](/iis) (或 Azure App Service) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上執行時，如果無法啟動處理序，[模組](xref:host-and-deploy/aspnet-core-module)會傳回 *502.5 - 處理序失敗*。 如需詳細資訊，請參閱<xref:test/troubleshoot-azure-iis>。

## <a name="database-error-page"></a>資料庫錯誤頁面

資料庫開發人員頁面例外狀況篩選 `AddDatabaseDeveloperPageExceptionFilter` 會捕捉與資料庫相關的例外狀況，這些例外狀況可以使用 Entity Framework Core 遷移來解決。 當這些例外狀況發生時，會產生 HTML 回應，並提供解決問題的可能動作詳細資料。 此頁面只會在開發環境中啟用。 Razor指定個別使用者帳戶時，ASP.NET Core Pages 範本會產生下列程式碼：

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>例外狀況篩選條件

在 MVC 應用程式中，您能以全域設定例外狀況篩選條件，或是以每個控制器或每個動作為基礎的方式設定。 在 Razor 頁面應用程式中，可以設定全域或依頁面的模型。 這些篩選器會處理在控制器動作或其他篩選準則執行期間發生的任何未處理例外狀況。 如需詳細資訊，請參閱<xref:mvc/controllers/filters#exception-filters>。

例外狀況篩選準則適用于捕捉 MVC 動作內發生的例外狀況，但是它們並不像內建的 [例外狀況處理中介軟體](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)那麼有彈性 `UseExceptionHandler` 。 `UseExceptionHandler`除非您需要根據選擇的 MVC 動作，以不同的方式執行錯誤處理，否則建議使用。

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>模型狀態錯誤

如需如何處理模型狀態錯誤的相關資訊，請參閱[模型繫結](xref:mvc/models/model-binding)和[模型驗證](xref:mvc/models/validation)。

## <a name="additional-resources"></a>其他資源

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

由  [Tom Dykstra](https://github.com/tdykstra/)和 [Steve Smith](https://ardalis.com/)

本文涵蓋 ASP.NET Core web 應用程式中處理錯誤的常見方法。 請參閱 <xref:web-api/handle-errors> ，以取得 Web api。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。  ([如何下載](xref:index#how-to-download-a-sample)。 ) 

## <a name="developer-exception-page"></a>開發人員例外狀況頁面

「開發人員例外狀況頁面」會顯示要求例外狀況的詳細資訊。 ASP.NET Core 範本會產生下列程式碼：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

當應用程式在 [開發環境](xref:fundamentals/environments)中執行時，上述程式碼會啟用開發人員例外狀況頁面。

範本會在 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 任何中介軟體之前放置，因此例外狀況會在接下來的中介軟體中捕捉。

上述 **程式碼只會在應用程式于開發環境中執行時，才會** 啟用開發人員例外狀況頁面。 當應用程式在生產環境中執行時，不應公開顯示詳細的例外狀況資訊。 如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。

開發人員例外狀況頁面包含下列有關例外狀況和要求的資訊：

* 堆疊追蹤
* 查詢字串參數（如果有的話）
* Cookie如果有的話
* 標題

## <a name="exception-handler-page"></a>例外處理常式頁面

若要針對生產環境設定自訂錯誤處理頁面，請使用例外狀況處理中介軟體。 中介軟體：

* 攔截並記錄例外狀況。
* 可在所指示頁面或控制器的替代管線中重新執行要求。 如果回應已啟動，就不會重新執行要求。 範本產生的程式碼會重新執行要求的 `/Error` 。

在下列範例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 會在非開發環境中加入例外狀況處理中介軟體：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorPages 應用程式範本會 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> `ErrorModel` 在 [ *pages* ] 資料夾中提供錯誤頁面 () 和類別 () 。 在 MVC 應用程式中，專案範本會在 Home 控制器中包含錯誤動作方法和錯誤視圖。

請勿將錯誤處理常式動作方法標記為 HTTP 方法屬性，例如 `HttpGet` 。 明確的動詞命令可防止某些要求取得方法。 如果未經驗證的使用者應該會看到錯誤視圖，則允許匿名存取方法。

### <a name="access-the-exception"></a>存取例外狀況

使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 來存取例外狀況或和錯誤處理常式控制器或頁面中的原始要求路徑：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> 請 **勿** 提供敏感性的錯誤資訊給用戶端。 提供錯誤有安全性風險。

若要觸發先前的例外狀況處理頁面，請將環境設定為生產環境，並強制執行例外狀況。

## <a name="exception-handler-lambda"></a>例外處理常式 Lambda

[自訂例外處理常式頁面](#exception-handler-page)的替代方法，便是將 Lambda 提供給 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。 使用 lambda 可讓您在傳回回應之前存取錯誤。

以下是將 Lambda 用於例外狀況處理的範例：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

在上述程式碼中， `await context.Response.WriteAsync(new string(' ', 512));` 會加入，因此 Internet Explorer 瀏覽器會顯示錯誤訊息，而不是 IE 錯誤訊息。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/16144)。

> [!WARNING]
> 請 **勿** 從 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 提供錯誤資訊給用戶端。 提供錯誤有安全性風險。

若要在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) \(英文\) 中查看例外狀況處理 Lambda 的結果，請使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 前置處理器指示詞，並選取首頁上的 [觸發例外狀況]。

## <a name="usestatuscodepages"></a>UseStatusCodePages

根據預設，ASP.NET Core 應用程式不會提供 HTTP 狀態碼 (例如「404 - 找不到」) 等狀態碼頁面。 應用程式會傳回狀態碼和空白回應主體。 若要提供狀態碼頁面，請使用狀態碼頁面中介軟體。

中介軟體是由 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 套件提供。

若要針對常見的錯誤狀態碼啟用預設的純文字處理常式，請呼叫 `Startup.Configure` 方法中的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

要求處理中介軟體 (例如靜態檔案中介軟體和 MVC 中介軟體) 之前，應該先呼叫 `UseStatusCodePages`。

`UseStatusCodePages`未使用時，流覽至不含端點的 URL 會傳回瀏覽器相依的錯誤訊息，指出找不到端點。 例如，流覽至 `Home/Privacy2` 。 當 `UseStatusCodePages` 呼叫時，瀏覽器會傳回：

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>具格式字串的 UseStatusCodePages

若要自訂回應內容類型和文字，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用內容類型和格式字串：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>具 Lambda 的 UseStatusCodePages

若要指定自訂錯誤處理和回應撰寫程式碼，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用 Lambda 運算式：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 擴充方法：

* 傳送「302 - 已找到」狀態碼傳送給用戶端。
* 將用戶端重新導向到 URL 範本中提供的位置。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

URL 範本可以針對狀態碼包含 `{0}` 預留位置，如範例所示。 如果 URL 範本的開頭是 (波狀符號 `~`) ， `~` 會由應用程式取代 `PathBase` 。 如果您指向應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。 如需 Razor 頁面範例，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 *pages/StatusCode. cshtml。*

此方法通常是在下列應用程式相關情況下使用：

* 應用程式應將用戶端重新導向至不同的端點時 (通常是在由其他應用程式處理錯誤的情況下)。 針對 Web 應用程式，用戶端的瀏覽器網址列會反映重新導向後的端點。
* 應用程式不應該保留並傳回原始狀態碼與初始重新導向回應時。

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 擴充方法：

* 傳回原始狀態碼給用戶端。
* 可透過使用替代路徑重新執行要求管線來產生回應本文。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

如果您指向應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。 請確定先放置，才能將 `UseStatusCodePagesWithReExecute` `UseRouting` 要求重新路由至狀態頁面。 如需 Razor 頁面範例，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 *pages/StatusCode. cshtml。*

此方法通常是在下列應用程式相關情況下使用：

* 在不重新導向至其他端點的情況下處理要求。 針對 Web 應用程式，用戶端的瀏覽器網址列會反映原始要求的端點。
* 保留並傳回原始狀態碼與回應時。

URL 和查詢字串範本可能會包含該狀態碼的預留位置 (`{0}`)。 URL 範本的開頭必須是斜線 (`/`)。 在路徑中使用預留位置時，請確認端點 (頁面或控制器) 可以處理路徑線段。 例如，錯誤的 Razor 頁面應接受具有指示詞的選擇性路徑區段值 `@page` ：

```cshtml
@page "{code?}"
```

處理錯誤的端點可以取得產生該錯誤的原始 URL，如下列範例所示：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>停用狀態碼頁面

若要停用 MVC 控制器或動作方法的狀態字碼頁面，請使用 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 屬性。

若要在 Razor 頁面處理常式方法或 MVC 控制器中停用特定要求的狀態字碼頁面，請使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> ：

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>例外狀況處理程式碼

例外狀況處理頁面中的程式碼，可擲回例外狀況。 一般來說，較好的做法是讓生產環境的錯誤頁面由純靜態內容組成。

### <a name="response-headers"></a>回應標頭

一旦傳送回應的標頭之後：

* 應用程式就無法變更回應的狀態碼。
* 無法執行任何例外狀況頁面或處理常式。 回應必須完成，否則會中止連線。

## <a name="server-exception-handling"></a>伺服器例外狀況處理

除了應用程式中的例外狀況處理邏輯之外，[HTTP 伺服器實作](xref:fundamentals/servers/index)也可以處理一些例外狀況。 如果伺服器在回應標頭傳送之前攔截到例外狀況，伺服器會傳送「500 - 內部伺服器錯誤」回應，且沒有回應本文。 如果伺服器在回應標頭傳送之後攔截到例外狀況，伺服器會關閉連線。 應用程式未處理的要求會由伺服器來處理。 當伺服器處理要求時，任何發生的例外狀況均由伺服器的例外狀況處理功能來處理。 應用程式的自訂錯誤頁面、例外狀況處理中介軟體或篩選條件並不會影響此行為。

## <a name="startup-exception-handling"></a>啟動例外狀況處理

只有裝載層可以處理應用程式啟動期間發生的例外狀況。 可以將主機設定為會[擷取啟動錯誤](xref:fundamentals/host/web-host#capture-startup-errors)和[擷取詳細錯誤](xref:fundamentals/host/web-host#detailed-errors)。

只有在錯誤是於主機位址/連接埠繫結之後發生的情況下，裝載層才能顯示已擷取之啟動錯誤的錯誤頁面。 如果繫結失敗：

* 裝載層會記錄重大例外狀況。
* Dotnet 會處理損毀狀況。
* 當 HTTP 伺服器是 [Kestrel](xref:fundamentals/servers/kestrel) 時，不會顯示任何錯誤頁面。

在 [IIS](/iis) (或 Azure App Service) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上執行時，如果無法啟動處理序，[模組](xref:host-and-deploy/aspnet-core-module)會傳回 *502.5 - 處理序失敗*。 如需詳細資訊，請參閱<xref:test/troubleshoot-azure-iis>。

## <a name="database-error-page"></a>資料庫錯誤頁面

資料庫錯誤頁面中介軟體會捕捉資料庫相關的例外狀況，這些例外狀況可使用 Entity Framework 遷移來解決。 發生這些例外狀況時，系統會產生具解決該問題之可能動作詳細資料的 HTML 回應。 此頁面僅應該於開發環境中啟用。 將程式碼加入 `Startup.Configure` 來啟用該頁面：

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> 需要 [AspNetCore Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 套件。

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>例外狀況篩選條件

在 MVC 應用程式中，您能以全域設定例外狀況篩選條件，或是以每個控制器或每個動作為基礎的方式設定。 在 Razor 頁面應用程式中，可以設定全域或依頁面的模型。 這些篩選會處理在控制器動作或其他篩選條件執行期間發生但的任何未處理例外狀況。 如需詳細資訊，請參閱<xref:mvc/controllers/filters#exception-filters>。

> [!TIP]
> 例外狀況篩選條件適合用來截獲 MVC 動作中發生的例外狀況，但是它們並不像例外狀況處理中介軟體那麼有彈性。 我們建議使用中介軟體。 請只在需要根據已選擇的 MVC 動作執行不同的錯誤處理時，才使用篩選條件。

## <a name="model-state-errors"></a>模型狀態錯誤

如需如何處理模型狀態錯誤的相關資訊，請參閱[模型繫結](xref:mvc/models/model-binding)和[模型驗證](xref:mvc/models/validation)。

## <a name="additional-resources"></a>其他資源

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
