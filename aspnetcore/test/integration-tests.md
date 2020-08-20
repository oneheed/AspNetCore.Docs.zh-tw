---
title: ASP.NET Core 中的整合測試
author: rick-anderson
description: 了解整合測試如何確保應用程式的元件在基礎結構層級 (包括資料庫、檔案系統和網路) 正確運作。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: b06c06fb5e525a0bdc3df1de50236fa8f76daca9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635108"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core 中的整合測試

[Javier Calvarro Nelson](https://github.com/javiercn)、 [Steve Smith](https://ardalis.com/)和[Jos van der 磚](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

整合測試可確保應用程式的元件在包含應用程式支援基礎結構的層級（例如資料庫、檔案系統和網路）正常運作。 ASP.NET Core 使用單元測試架構搭配測試 web 主機和記憶體中的測試伺服器來支援整合測試。

本主題假設您對單元測試有基本的瞭解。 如果不熟悉測試概念，請參閱 [.Net Core 中的單元測試和 .NET Standard](/dotnet/core/testing/) 主題及其連結的內容。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

範例應用程式是 Razor 頁面應用程式，並假設對頁面有基本的瞭解 Razor 。 如果不熟悉 Razor 頁面，請參閱下列主題：

* [頁面簡介 Razor](xref:razor-pages/index)
* [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 頁面單元測試](xref:test/razor-pages-tests)

> [!NOTE]
> 針對測試 Spa，我們建議使用 [Selenium](https://www.seleniumhq.org/)之類的工具，這可以將瀏覽器自動化。

## <a name="introduction-to-integration-tests"></a>整合測試簡介

整合測試會評估應用程式元件在更廣泛的層級上，而不是 [單元測試](/dotnet/core/testing/)。 單元測試是用來測試隔離的軟體元件，例如個別的類別方法。 整合測試會確認兩個或多個應用程式元件會一起運作，以產生預期的結果，可能包括完整處理要求所需的每個元件。

這些廣泛的測試是用來測試應用程式的基礎結構和整個架構，通常包括下列元件：

* 資料庫
* 檔案系統
* 網路設備
* 要求-回應管線

單元測試使用製造的元件（稱為 *fakes* 或 *mock 物件*）來取代基礎結構元件。

相對於單元測試，整合測試：

* 使用應用程式在生產環境中使用的實際元件。
* 需要更多程式碼和資料處理。
* 執行需要較長的時間。

因此，請將整合測試的使用限制在最重要的基礎結構案例中。 如果您可以使用單元測試或整合測試來測試行為，請選擇單元測試。

> [!TIP]
> 請勿針對每個可能的資料和檔案存取，使用資料庫和檔案系統來撰寫整合測試。 無論應用程式之間有多少位置與資料庫和檔案系統互動，一組專注的讀取、寫入、更新和刪除整合測試，通常都能充分測試資料庫和檔案系統元件。 針對與這些元件互動的方法邏輯，使用單元測試進行常式測試。 在單元測試中，使用基礎結構 fakes/模擬會導致更快速的測試執行。

> [!NOTE]
> 在整合測試的討論中，測試過的專案通常稱為 *受測試的系統*，或簡稱為「SUT」。
>
> *本主題中使用 "SUT" 來參考經過測試的 ASP.NET Core 應用程式。*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 整合測試

ASP.NET Core 中的整合測試需要下列各項：

* 測試專案可用來包含和執行測試。 測試專案具有此 SUT 的參考。
* 測試專案會建立適用于該 SUT 的測試 web 主機，並使用測試伺服器用戶端來處理與該 SUT 的要求和回應。
* 測試執行器會用來執行測試並報告測試結果。

整合測試會遵循一連串的事件，其中包含一般的 *排列*、 *Act*和 *Assert* 測試步驟：

1. 已設定 SUT 的 web 主機。
1. 建立測試伺服器用戶端以將要求提交給應用程式。
1. 執行「 *排列* 測試」步驟：測試應用程式準備要求。
1. 執行 *Act* 測試步驟：用戶端會提交要求並接收回應。
1. 執行*Assert*測試步驟：*實際*的回應會根據*預期*的回應，驗證為*通過*或*失敗*。
1. 此程式會繼續執行，直到執行所有測試為止。
1. 系統會報告測試結果。

測試 web 主機的設定方式通常與測試回合的應用程式一般 web 主機不同。 例如，可能會使用不同的資料庫或不同的應用程式設定來進行測試。

基礎結構元件，例如測試 web 主機和記憶體中的測試伺服器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) ，是由 AspNetCore 所提供，或由 [測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝所管理。 使用這個封裝可簡化測試的建立和執行。

`Microsoft.AspNetCore.Mvc.Testing`封裝會處理下列工作：

* 從 d)  (將相依性檔案從 *.deps*複製到測試專案的*bin*目錄。
* 將 [內容根目錄](xref:fundamentals/index#content-root) 設定為 SUT 的專案根目錄，以便在執行測試時找到靜態檔案和頁面/瀏覽器。
* 提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 類別，以簡化使用來啟動載入的工作 `TestServer` 。

[單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)檔會描述如何設定測試專案和測試執行器，以及如何執行測試和建議的詳細指示，以瞭解如何命名測試和測試類別。

> [!NOTE]
> 建立應用程式的測試專案時，請將整合測試中的單元測試分成不同的專案。 這有助於確保基礎結構測試元件不會不慎包含在單元測試中。 單元和整合測試的分隔也可讓您控制要執行的測試集。

Razor頁面應用程式和 MVC 應用程式的測試設定幾乎沒有任何差異。 唯一的差異在於測試的命名方式。 在 Razor 頁面應用程式中，頁面端點的測試通常會在頁面模型類別之後命名 (例如，用 `IndexPageTests` 來測試索引頁面) 的元件整合。 在 MVC 應用程式中，通常會依控制器類別來組織測試，並以測試的控制器命名 (例如， `HomeControllerTests` 測試主控制器) 的元件整合。

## <a name="test-app-prerequisites"></a>測試應用程式必要條件

測試專案必須：

* 參考 [AspNetCore 的測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝。
* 在專案檔 () 中指定 Web SDK `<Project Sdk="Microsoft.NET.Sdk.Web">` 。

您可以在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中看到這些必要條件。 檢查 *測試/ Razor PagesProject. 測試/ Razor PagesProject* 。 範例應用程式會使用 [xUnit](https://xunit.github.io/) 測試架構和 [AngleSharp](https://anglesharp.github.io/) 剖析器程式庫，因此範例應用程式也會參考：

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit。 visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

測試中也會使用 Entity Framework Core。 應用程式參考：

* [AspNetCore. Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [AspNetCore Identity 。Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.entityframeworkcore 工具](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>SUT 環境

如果未設定 SUT 的 [環境](xref:fundamentals/environments) ，則環境會預設為開發。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>使用預設 WebApplicationFactory 的基本測試

[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用來建立整合測試的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。 `TEntryPoint` 是此 SUT 的進入點類別，通常是 `Startup` 類別。

測試類別會將 *類別裝置* 介面 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 來指出類別包含測試，並在類別中的測試之間提供共用物件實例。

下列測試類別會 `BasicTests` 使用 `WebApplicationFactory` 來啟動載入並提供測試方法的 [HttpClient](/dotnet/api/system.net.http.httpclient) `Get_EndpointsReturnSuccessAndCorrectContentType` 。 方法會檢查回應狀態碼是否成功 (狀態碼在 200-299) 範圍內，且 `Content-Type` 標頭 `text/html; charset=utf-8` 適用于數個應用程式頁面。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 會建立的實例 `HttpClient` ，它會自動遵循重新導向和處理 cookie 。

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

依預設，在 cookie 啟用 [GDPR 同意原則](xref:security/gdpr) 時，不會在要求之間保留非必要的。 若要保留非必要的 cookie ，例如 TempData 提供者所使用的，請在您的測試中將它們標示為必要。 如需將 a 標示 cookie 為必要的指示，請參閱 [重要的 cookie s](xref:security/gdpr#essential-cookies)。

## <a name="customize-webapplicationfactory"></a>自訂 WebApplicationFactory

您可以藉由繼承 `WebApplicationFactory` 來建立一或多個自訂處理站，以獨立于測試類別建立 Web 主機設定：

1. 繼承 `WebApplicationFactory` 並覆寫 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)可讓您使用[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)設定服務集合：

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的資料庫植入是由 `InitializeDbForTests` 方法執行。 [整合測試範例：測試應用程式組織](#test-app-organization)一節中會說明方法。

   已在其方法中註冊的 SUT 資料庫內容 `Startup.ConfigureServices` 。 測試應用程式的 `builder.ConfigureServices` 回呼會在*after*應用程式的程式 `Startup.ConfigureServices` 代碼執行之後執行。 執行順序是 ASP.NET Core 3.0 版本之 [泛型主機](xref:fundamentals/host/generic-host) 的重大變更。 若要針對測試使用與應用程式資料庫不同的資料庫，則必須在中取代應用程式的資料庫內容 `builder.ConfigureServices` 。

   對於仍在使用 [Web 主機](xref:fundamentals/host/web-host)的 SUTs，測試應用程式的 `builder.ConfigureServices` 回呼會在 SUT 的程式碼 *之前* 執行 `Startup.ConfigureServices` 。 測試應用程式的 `builder.ConfigureTestServices` 回呼會 *在之後*執行。

   範例應用程式會尋找資料庫內容的服務描述項，並使用描述項來移除服務註冊。 接下來，factory 會加入新 `ApplicationDbContext` 的，其會使用記憶體內部資料庫進行測試。

   若要連接到與記憶體內部資料庫不同的資料庫，請變更將 `UseInMemoryDatabase` 內容連接到不同資料庫的呼叫。 若要使用 SQL Server 測試資料庫：

   * 參考專案檔中的[microsoft.entityframeworkcore。](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
   * `UseSqlServer`使用連接字串來呼叫資料庫。

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. `CustomWebApplicationFactory`在測試類別中使用自訂。 下列範例會使用類別中的 factory `IndexPageTests` ：

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   範例應用程式的用戶端設定為防止 `HttpClient` 下列重新導向。 如稍後在 [Mock 驗證](#mock-authentication) 區段中所述，這會允許測試檢查應用程式第一個回應的結果。 第一個回應是其中許多測試中有標頭的重新導向 `Location` 。

3. 一般的測試會使用 `HttpClient` 和 helper 方法來處理要求和回應：

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

對所有的 SUT 進行 POST 要求都必須滿足應用程式 [資料保護 antiforgery 系統](xref:security/data-protection/introduction)自動進行的 antiforgery 檢查。 為了排列測試的 POST 要求，測試應用程式必須：

1. 提出頁面要求。
1. cookie從回應中剖析 antiforgery 和要求驗證 token。
1. 使用 antiforgery cookie 和要求驗證權杖來發出 POST 要求。

`SendAsync`Helper */) HttpClientExtensions*中的 helper 擴充方法 (協助程式/HtmlHelpers，以及 `GetDocumentAsync` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的協助程式方法 (helper */) * ，請使用[AngleSharp](https://anglesharp.github.io/)剖析器，利用下列方法來處理 antiforgery 檢查：

* `GetDocumentAsync`：接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 並傳回 `IHtmlDocument` 。 `GetDocumentAsync` 使用可根據原始的來準備 *虛擬回應* 的 factory `HttpResponseMessage` 。 如需詳細資訊，請參閱 [AngleSharp 檔](https://github.com/AngleSharp/AngleSharp#documentation)。
* `SendAsync``HttpClient`撰寫[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)並呼叫 SendAsync 的擴充方法[ (HttpRequestMessage) ](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)將要求提交至 SUT。 `SendAsync`接受 HTML 表單 (`IHtmlFormElement`) 的多載，以及下列各項：
  * 表單 () 的 [提交] 按鈕 `IHtmlElement`
  * 表單值集合 (`IEnumerable<KeyValuePair<string, string>>`) 
  * 提交按鈕 (`IHtmlElement`) 和表單值 (`IEnumerable<KeyValuePair<string, string>>`) 

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) 是協力廠商剖析程式庫，用於本主題和範例應用程式中的示範用途。 ASP.NET Core 應用程式的整合測試不支援或不需要 AngleSharp。 您可以使用其他剖析器，例如 [ (HAP) 的 Html 敏捷套件 ](https://html-agility-pack.net/)。 另一種方法是撰寫程式碼來處理 antiforgery 系統的要求驗證權杖，並 cookie 直接 antiforgery。

## <a name="customize-the-client-with-withwebhostbuilder"></a>使用 WithWebHostBuilder 自訂用戶端

在測試方法中需要進行其他設定時， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 會建立一個新的， `WebApplicationFactory` 其中包含由設定進一步自訂的 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。

`Post_DeleteMessageHandler_ReturnsRedirectToRoot`[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的測試方法會示範的使用方式 `WithWebHostBuilder` 。 這項測試會在 SUT 中觸發表單提交，在資料庫中執行記錄刪除。

因為類別中的另一個測試 `IndexPageTests` 會執行刪除資料庫中所有記錄的作業，而且可能會在方法之前執行，所以會 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 在此測試方法中重新植入資料庫，以確保有記錄存在，以便讓 SUT 刪除。 在 sut 的 `messages` 要求中，會模擬在 sut 中選取表單的第一個 [刪除] 按鈕：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>用戶端選項

下表顯示建立實例時可使用的預設 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) `HttpClient` 。

| 選項 | 描述 | 預設 |
| ------ | ----------- | ------- |
| [Request.allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 取得或設定 `HttpClient` 實例是否應該自動遵循重新導向回應。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 取得或設定實例的基底位址 `HttpClient` 。 | `http://localhost` |
| [句 Cookie 柄](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 取得或設定 `HttpClient` 實例是否應該處理 cookie s。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 取得或設定實例應遵循的重新導向回應數目上限 `HttpClient` 。 | 7 |

建立 `WebApplicationFactoryClientOptions` 類別，並將它傳遞給 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (預設值會顯示在程式碼範例) 中：

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>插入 mock 服務

您可以在主機建立器上呼叫 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，以在測試中覆寫服務。 **若要插入模擬服務，則必須具有 `Startup` 具有方法的類別 `Startup.ConfigureServices` 。**

範例 SUT 包含會傳回報價的範圍服務。 當要求索引頁面時，引號會內嵌在索引頁面的隱藏欄位中。

*Services/IQuoteService .cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService .cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index .cs*：

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

執行 SUT 應用程式時，會產生下列標記：

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

若要在整合測試中測試服務和報價插入，模擬服務會由測試插入至 SUT。 模擬服務會將應用程式取代為 `QuoteService` 測試應用程式所提供的服務，稱為 `TestQuoteService` ：

*IntegrationTests.IndexPageTests.cs*：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` 會呼叫，且已註冊範圍服務：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

測試執行期間所產生的標記會反映所提供的報價文字 `TestQuoteService` ，因此判斷提示會通過：

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Mock 驗證

類別中的測試 `AuthTests` 會檢查安全的端點：

* 將未經驗證的使用者重新導向至應用程式的登入頁面。
* 傳回已驗證使用者的內容。

在 SUT 中， `/SecurePage` 頁面會使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 慣例，將 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 套用至頁面。 如需詳細資訊，請參閱[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 測試中， [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 會設定為 [不允許重新導向]，方法是將 [request.allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 設定為 `false` ：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

藉由不允許用戶端遵循重新導向，可進行下列檢查：

* 在重新導向至登入頁面之後，您可以根據預期的 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode) 來檢查您所傳回的狀態碼，而不是最後的狀態碼，這會是 [HttpStatusCode。確定](/dotnet/api/system.net.httpstatuscode)。
* `Location`檢查回應標頭中的標頭值，以確認它的開頭 `http://localhost/Identity/Account/Login` 不是最後一個登入頁面回應，而且 `Location` 標頭不會出現。

測試應用程式可以在 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模擬，以便測試驗證和授權的層面。 基本案例會傳回 [AuthenticateResult。 Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler`當驗證配置設定為的註冊位置時，會呼叫來驗證 `Test` 使用者 `AddAuthentication` `ConfigureTestServices` 。 `Test`配置必須符合您的應用程式所預期的配置是很重要的。 否則，驗證將無法運作。

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

如需的詳細資訊 `WebApplicationFactoryClientOptions` ，請參閱 [用戶端選項](#client-options) 一節。

## <a name="set-the-environment"></a>設定環境

根據預設，已將 SUT 的主機和應用程式環境設定為使用開發環境。 若要在使用時覆寫 SUT 的環境 `IHostBuilder` ：

* 設定 `ASPNETCORE_ENVIRONMENT` 環境變數 (例如，、 `Staging` `Production` 或其他自訂值，例如 `Testing`) 。
* `CreateHostBuilder`在測試應用程式中覆寫，以讀取前面加上的環境變數 `ASPNETCORE` 。

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

如果 SUT 使用 () 的 Web 主機 `IWebHostBuilder` ，請覆寫 `CreateWebHostBuilder` ：

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>測試基礎結構如何推斷應用程式內容根路徑

此函式會藉 `WebApplicationFactory` 由在包含整合測試與元件相等的元件上搜尋[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) ，來推斷應用程式[內容的根](xref:fundamentals/index#content-root)路徑 `TEntryPoint` `System.Reflection.Assembly.FullName` 。 如果找不到具有正確索引鍵的屬性，則會 `WebApplicationFactory` 切換回以搜尋方案檔 (*.Sln*) 並將 `TEntryPoint` 元件名稱附加至方案目錄。 應用程式根目錄 (內容根路徑) 用來探索視圖和內容檔案。

## <a name="disable-shadow-copying"></a>停用陰影複製

陰影複製會導致測試在和輸出目錄不同的目錄中執行。 若要讓測試正常運作，必須停用陰影複製。 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)會使用 xUnit 並停用 xUnit 的陰影複製，方法是在具有正確設定的檔案*上包含xunit.runner.js* 。 如需詳細資訊，請參閱 [使用 JSON 設定 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。

使用下列內容，將檔案 * 上的xunit.runner.js* 新增至測試專案的根目錄：

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>物件的處置

`IClassFixture`執行實施測試之後，當 xUnit 處置[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)時，會處置[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。 如果開發人員所具現化的物件需要處置，請在執行時處置它們 `IClassFixture` 。 如需詳細資訊，請參閱 [執行 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

## <a name="integration-tests-sample"></a>整合測試範例

[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)是由兩個應用程式所組成：

| 應用程式 | 專案目錄 | 描述 |
| --- | ----------------- | ----------- |
|  (在 SUT) 的訊息應用程式 | *src/ Razor PagesProject* | 允許使用者新增、刪除、刪除所有訊息，以及分析訊息。 |
| 測試應用程式 | *測試/ Razor PagesProject* | 用於整合測試的 SUT。 |

您可以使用 IDE 的內建測試功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）來執行測試。 如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesProject* ] 目錄中的命令提示字元執行下列命令：

```console
dotnet test
```

### <a name="message-app-sut-organization"></a> (的 SUT) 組織的訊息應用程式

SUT 是 Razor 具有下列特性的頁面訊息系統：

* 應用程式的 [索引] 頁面 (*pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法，可控制訊息的新增、刪除和分析 (每個訊息) 的平均單字。
* 訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。 `Text`屬性是必要的，且限制為200個字元。
* 訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/)&#8224; 來儲存。
* 應用程式在其資料庫內容類別中包含 (DAL) 的資料存取層， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。
* 如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。
* 應用程式包含 `/SecurePage` 只能由已驗證的使用者存取的。

&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。 本主題使用 [xUnit](https://xunit.github.io/) 測試架構。 跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。

雖然應用程式不會使用存放庫模式，而不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。 如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 [測試控制器邏輯](/aspnet/core/mvc/controllers/testing) (範例會) 執行儲存機制模式。

### <a name="test-app-organization"></a>測試應用程式組織

測試應用程式是 [ *測試/ Razor PagesProject* ] 目錄中的主控台應用程式。

| 測試應用程式目錄 | 描述 |
| ------------------ | ----------- |
| *AuthTests* | 包含的測試方法：<ul><li>未經驗證的使用者存取安全的頁面。</li><li>使用模擬以已驗證的使用者存取安全的頁面 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> 。</li><li>取得 GitHub 使用者設定檔，並檢查設定檔的使用者登入。</li></ul> |
| *BasicTests* | 包含路由和內容類型的測試方法。 |
| *IntegrationTests* | 包含使用自訂類別之 [索引] 頁面的整合測試 `WebApplicationFactory` 。 |
| *協助程式/公用程式* | <ul><li>*Utilities.cs* 包含 `InitializeDbForTests` 用來將測試資料植入資料庫的方法。</li><li>*HtmlHelpers.cs* 會提供方法，以傳回 `IHtmlDocument` 測試方法所使用的 AngleSharp。</li><li>*HttpClientExtensions.cs* 會提供的多載， `SendAsync` 以將要求提交至 SUT。</li></ul> |

測試架構為 [xUnit](https://xunit.github.io/)。 整合測試是使用 [AspNetCore TestHost](/dotnet/api/microsoft.aspnetcore.testhost)，其中包含 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。 由於使用 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝來設定測試主機和測試伺服器，因此 `TestHost` 和套件不需要測試應用程式的專案檔 `TestServer` 或開發人員在測試應用程式中進行直接封裝參考。

**植入資料庫以進行測試**

在測試執行之前，整合測試通常需要資料庫中的小型資料集。 例如，刪除測試會呼叫資料庫記錄刪除，因此資料庫必須至少有一筆記錄，刪除要求才能成功。

範例應用程式會使用 *Utilities.cs* 中的三個訊息來植入資料庫，測試可在執行時使用這些訊息：

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

已在其方法中註冊的 SUT 資料庫內容 `Startup.ConfigureServices` 。 測試應用程式的 `builder.ConfigureServices` 回呼會在*after*應用程式的程式 `Startup.ConfigureServices` 代碼執行之後執行。 若要使用不同的資料庫進行測試，必須在中取代應用程式的資料庫內容 `builder.ConfigureServices` 。 如需詳細資訊，請參閱 [自訂 WebApplicationFactory](#customize-webapplicationfactory) 一節。

對於仍在使用 [Web 主機](xref:fundamentals/host/web-host)的 SUTs，測試應用程式的 `builder.ConfigureServices` 回呼會在 SUT 的程式碼 *之前* 執行 `Startup.ConfigureServices` 。 測試應用程式的 `builder.ConfigureTestServices` 回呼會 *在之後*執行。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

整合測試可確保應用程式的元件在包含應用程式支援基礎結構的層級（例如資料庫、檔案系統和網路）正常運作。 ASP.NET Core 使用單元測試架構搭配測試 web 主機和記憶體中的測試伺服器來支援整合測試。

本主題假設您對單元測試有基本的瞭解。 如果不熟悉測試概念，請參閱 [.Net Core 中的單元測試和 .NET Standard](/dotnet/core/testing/) 主題及其連結的內容。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

範例應用程式是 Razor 頁面應用程式，並假設對頁面有基本的瞭解 Razor 。 如果不熟悉 Razor 頁面，請參閱下列主題：

* [頁面簡介 Razor](xref:razor-pages/index)
* [開始使用 Razor 頁面](xref:tutorials/razor-pages/razor-pages-start)
* [Razor 頁面單元測試](xref:test/razor-pages-tests)

> [!NOTE]
> 針對測試 Spa，我們建議使用 [Selenium](https://www.seleniumhq.org/)之類的工具，這可以將瀏覽器自動化。

## <a name="introduction-to-integration-tests"></a>整合測試簡介

整合測試會評估應用程式元件在更廣泛的層級上，而不是 [單元測試](/dotnet/core/testing/)。 單元測試是用來測試隔離的軟體元件，例如個別的類別方法。 整合測試會確認兩個或多個應用程式元件會一起運作，以產生預期的結果，可能包括完整處理要求所需的每個元件。

這些廣泛的測試是用來測試應用程式的基礎結構和整個架構，通常包括下列元件：

* 資料庫
* 檔案系統
* 網路設備
* 要求-回應管線

單元測試使用製造的元件（稱為 *fakes* 或 *mock 物件*）來取代基礎結構元件。

相對於單元測試，整合測試：

* 使用應用程式在生產環境中使用的實際元件。
* 需要更多程式碼和資料處理。
* 執行需要較長的時間。

因此，請將整合測試的使用限制在最重要的基礎結構案例中。 如果您可以使用單元測試或整合測試來測試行為，請選擇單元測試。

> [!TIP]
> 請勿針對每個可能的資料和檔案存取，使用資料庫和檔案系統來撰寫整合測試。 無論應用程式之間有多少位置與資料庫和檔案系統互動，一組專注的讀取、寫入、更新和刪除整合測試，通常都能充分測試資料庫和檔案系統元件。 針對與這些元件互動的方法邏輯，使用單元測試進行常式測試。 在單元測試中，使用基礎結構 fakes/模擬會導致更快速的測試執行。

> [!NOTE]
> 在整合測試的討論中，測試過的專案通常稱為 *受測試的系統*，或簡稱為「SUT」。
>
> *本主題中使用 "SUT" 來參考經過測試的 ASP.NET Core 應用程式。*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 整合測試

ASP.NET Core 中的整合測試需要下列各項：

* 測試專案可用來包含和執行測試。 測試專案具有此 SUT 的參考。
* 測試專案會建立適用于該 SUT 的測試 web 主機，並使用測試伺服器用戶端來處理與該 SUT 的要求和回應。
* 測試執行器會用來執行測試並報告測試結果。

整合測試會遵循一連串的事件，其中包含一般的 *排列*、 *Act*和 *Assert* 測試步驟：

1. 已設定 SUT 的 web 主機。
1. 建立測試伺服器用戶端以將要求提交給應用程式。
1. 執行「 *排列* 測試」步驟：測試應用程式準備要求。
1. 執行 *Act* 測試步驟：用戶端會提交要求並接收回應。
1. 執行*Assert*測試步驟：*實際*的回應會根據*預期*的回應，驗證為*通過*或*失敗*。
1. 此程式會繼續執行，直到執行所有測試為止。
1. 系統會報告測試結果。

測試 web 主機的設定方式通常與測試回合的應用程式一般 web 主機不同。 例如，可能會使用不同的資料庫或不同的應用程式設定來進行測試。

基礎結構元件，例如測試 web 主機和記憶體中的測試伺服器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) ，是由 AspNetCore 所提供，或由 [測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝所管理。 使用這個封裝可簡化測試的建立和執行。

`Microsoft.AspNetCore.Mvc.Testing`封裝會處理下列工作：

* 從 d)  (將相依性檔案從 *.deps*複製到測試專案的*bin*目錄。
* 將 [內容根目錄](xref:fundamentals/index#content-root) 設定為 SUT 的專案根目錄，以便在執行測試時找到靜態檔案和頁面/瀏覽器。
* 提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 類別，以簡化使用來啟動載入的工作 `TestServer` 。

[單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)檔會描述如何設定測試專案和測試執行器，以及如何執行測試和建議的詳細指示，以瞭解如何命名測試和測試類別。

> [!NOTE]
> 建立應用程式的測試專案時，請將整合測試中的單元測試分成不同的專案。 這有助於確保基礎結構測試元件不會不慎包含在單元測試中。 單元和整合測試的分隔也可讓您控制要執行的測試集。

Razor頁面應用程式和 MVC 應用程式的測試設定幾乎沒有任何差異。 唯一的差異在於測試的命名方式。 在 Razor 頁面應用程式中，頁面端點的測試通常會在頁面模型類別之後命名 (例如，用 `IndexPageTests` 來測試索引頁面) 的元件整合。 在 MVC 應用程式中，通常會依控制器類別來組織測試，並以測試的控制器命名 (例如， `HomeControllerTests` 測試主控制器) 的元件整合。

## <a name="test-app-prerequisites"></a>測試應用程式必要條件

測試專案必須：

* 參考下列套件：
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [AspNetCore 進行測試](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* 在專案檔 () 中指定 Web SDK `<Project Sdk="Microsoft.NET.Sdk.Web">` 。 參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)時，需要 Web SDK。

您可以在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中看到這些必要條件。 檢查 *測試/ Razor PagesProject. 測試/ Razor PagesProject* 。 範例應用程式會使用 [xUnit](https://xunit.github.io/) 測試架構和 [AngleSharp](https://anglesharp.github.io/) 剖析器程式庫，因此範例應用程式也會參考：

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit。 visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 環境

如果未設定 SUT 的 [環境](xref:fundamentals/environments) ，則環境會預設為開發。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>使用預設 WebApplicationFactory 的基本測試

[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用來建立整合測試的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。 `TEntryPoint` 是此 SUT 的進入點類別，通常是 `Startup` 類別。

測試類別會將 *類別裝置* 介面 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 來指出類別包含測試，並在類別中的測試之間提供共用物件實例。

下列測試類別會 `BasicTests` 使用 `WebApplicationFactory` 來啟動載入並提供測試方法的 [HttpClient](/dotnet/api/system.net.http.httpclient) `Get_EndpointsReturnSuccessAndCorrectContentType` 。 方法會檢查回應狀態碼是否成功 (狀態碼在 200-299) 範圍內，且 `Content-Type` 標頭 `text/html; charset=utf-8` 適用于數個應用程式頁面。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 會建立的實例 `HttpClient` ，它會自動遵循重新導向和處理 cookie 。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

依預設，在 cookie 啟用 [GDPR 同意原則](xref:security/gdpr) 時，不會在要求之間保留非必要的。 若要保留非必要的 cookie ，例如 TempData 提供者所使用的，請在您的測試中將它們標示為必要。 如需將 a 標示 cookie 為必要的指示，請參閱 [重要的 cookie s](xref:security/gdpr#essential-cookies)。

## <a name="customize-webapplicationfactory"></a>自訂 WebApplicationFactory

您可以藉由繼承 `WebApplicationFactory` 來建立一或多個自訂處理站，以獨立于測試類別建立 Web 主機設定：

1. 繼承 `WebApplicationFactory` 並覆寫 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)可讓您使用[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)設定服務集合，該集合會在應用程式之前執行 `Startup.ConfigureServices` 。 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)可讓您使用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)覆寫和修改服務集合中的已註冊服務：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的資料庫植入是由 `InitializeDbForTests` 方法執行。 [整合測試範例：測試應用程式組織](#test-app-organization)一節中會說明方法。

2. `CustomWebApplicationFactory`在測試類別中使用自訂。 下列範例會使用類別中的 factory `IndexPageTests` ：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   範例應用程式的用戶端設定為防止 `HttpClient` 下列重新導向。 如稍後在 [Mock 驗證](#mock-authentication) 區段中所述，這會允許測試檢查應用程式第一個回應的結果。 第一個回應是其中許多測試中有標頭的重新導向 `Location` 。

3. 一般的測試會使用 `HttpClient` 和 helper 方法來處理要求和回應：

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

對所有的 SUT 進行 POST 要求都必須滿足應用程式 [資料保護 antiforgery 系統](xref:security/data-protection/introduction)自動進行的 antiforgery 檢查。 為了排列測試的 POST 要求，測試應用程式必須：

1. 提出頁面要求。
1. cookie從回應中剖析 antiforgery 和要求驗證 token。
1. 使用 antiforgery cookie 和要求驗證權杖來發出 POST 要求。

`SendAsync`Helper */) HttpClientExtensions*中的 helper 擴充方法 (協助程式/HtmlHelpers，以及 `GetDocumentAsync` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的協助程式方法 (helper */) * ，請使用[AngleSharp](https://anglesharp.github.io/)剖析器，利用下列方法來處理 antiforgery 檢查：

* `GetDocumentAsync`：接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 並傳回 `IHtmlDocument` 。 `GetDocumentAsync` 使用可根據原始的來準備 *虛擬回應* 的 factory `HttpResponseMessage` 。 如需詳細資訊，請參閱 [AngleSharp 檔](https://github.com/AngleSharp/AngleSharp#documentation)。
* `SendAsync``HttpClient`撰寫[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)並呼叫 SendAsync 的擴充方法[ (HttpRequestMessage) ](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)將要求提交至 SUT。 `SendAsync`接受 HTML 表單 (`IHtmlFormElement`) 的多載，以及下列各項：
  * 表單 () 的 [提交] 按鈕 `IHtmlElement`
  * 表單值集合 (`IEnumerable<KeyValuePair<string, string>>`) 
  * 提交按鈕 (`IHtmlElement`) 和表單值 (`IEnumerable<KeyValuePair<string, string>>`) 

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) 是協力廠商剖析程式庫，用於本主題和範例應用程式中的示範用途。 ASP.NET Core 應用程式的整合測試不支援或不需要 AngleSharp。 您可以使用其他剖析器，例如 [ (HAP) 的 Html 敏捷套件 ](https://html-agility-pack.net/)。 另一種方法是撰寫程式碼來處理 antiforgery 系統的要求驗證權杖，並 cookie 直接 antiforgery。

## <a name="customize-the-client-with-withwebhostbuilder"></a>使用 WithWebHostBuilder 自訂用戶端

在測試方法中需要進行其他設定時， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 會建立一個新的， `WebApplicationFactory` 其中包含由設定進一步自訂的 [>iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。

`Post_DeleteMessageHandler_ReturnsRedirectToRoot`[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的測試方法會示範的使用方式 `WithWebHostBuilder` 。 這項測試會在 SUT 中觸發表單提交，在資料庫中執行記錄刪除。

因為類別中的另一個測試 `IndexPageTests` 會執行刪除資料庫中所有記錄的作業，而且可能會在方法之前執行，所以會 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 在此測試方法中重新植入資料庫，以確保有記錄存在，以便讓 SUT 刪除。 在 sut 的 `messages` 要求中，會模擬在 sut 中選取表單的第一個 [刪除] 按鈕：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>用戶端選項

下表顯示建立實例時可使用的預設 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) `HttpClient` 。

| 選項 | 描述 | 預設 |
| ------ | ----------- | ------- |
| [Request.allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 取得或設定 `HttpClient` 實例是否應該自動遵循重新導向回應。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 取得或設定實例的基底位址 `HttpClient` 。 | `http://localhost` |
| [句 Cookie 柄](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 取得或設定 `HttpClient` 實例是否應該處理 cookie s。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 取得或設定實例應遵循的重新導向回應數目上限 `HttpClient` 。 | 7 |

建立 `WebApplicationFactoryClientOptions` 類別，並將它傳遞給 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (預設值會顯示在程式碼範例) 中：

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>插入 mock 服務

您可以在主機建立器上呼叫 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，以在測試中覆寫服務。 **若要插入模擬服務，則必須具有 `Startup` 具有方法的類別 `Startup.ConfigureServices` 。**

範例 SUT 包含會傳回報價的範圍服務。 當要求索引頁面時，引號會內嵌在索引頁面的隱藏欄位中。

*Services/IQuoteService .cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService .cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index .cs*：

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

執行 SUT 應用程式時，會產生下列標記：

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

若要在整合測試中測試服務和報價插入，模擬服務會由測試插入至 SUT。 模擬服務會將應用程式取代為 `QuoteService` 測試應用程式所提供的服務，稱為 `TestQuoteService` ：

*IntegrationTests.IndexPageTests.cs*：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` 會呼叫，且已註冊範圍服務：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

測試執行期間所產生的標記會反映所提供的報價文字 `TestQuoteService` ，因此判斷提示會通過：

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Mock 驗證

類別中的測試 `AuthTests` 會檢查安全的端點：

* 將未經驗證的使用者重新導向至應用程式的登入頁面。
* 傳回已驗證使用者的內容。

在 SUT 中， `/SecurePage` 頁面會使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 慣例，將 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 套用至頁面。 如需詳細資訊，請參閱[ Razor 頁面授權慣例](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 測試中， [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 會設定為 [不允許重新導向]，方法是將 [request.allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 設定為 `false` ：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

藉由不允許用戶端遵循重新導向，可進行下列檢查：

* 在重新導向至登入頁面之後，您可以根據預期的 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode) 來檢查您所傳回的狀態碼，而不是最後的狀態碼，這會是 [HttpStatusCode。確定](/dotnet/api/system.net.httpstatuscode)。
* `Location`檢查回應標頭中的標頭值，以確認它的開頭 `http://localhost/Identity/Account/Login` 不是最後一個登入頁面回應，而且 `Location` 標頭不會出現。

測試應用程式可以在 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模擬，以便測試驗證和授權的層面。 基本案例會傳回 [AuthenticateResult。 Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler`當驗證配置設定為已註冊的位置時，會呼叫來驗證使用者 `Test` `AddAuthentication` `ConfigureTestServices` ：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

如需的詳細資訊 `WebApplicationFactoryClientOptions` ，請參閱 [用戶端選項](#client-options) 一節。

## <a name="set-the-environment"></a>設定環境

根據預設，已將 SUT 的主機和應用程式環境設定為使用開發環境。 若要覆寫 SUT 的環境：

* 設定 `ASPNETCORE_ENVIRONMENT` 環境變數 (例如，、 `Staging` `Production` 或其他自訂值，例如 `Testing`) 。
* `CreateWebHostBuilder`在測試應用程式中覆寫以讀取 `ASPNETCORE_ENVIRONMENT` 環境變數。

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

您也可以直接在自訂中的主機建立器上設定環境 <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> ：

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>測試基礎結構如何推斷應用程式內容根路徑

此函式會藉 `WebApplicationFactory` 由在包含整合測試與元件相等的元件上搜尋[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) ，來推斷應用程式[內容的根](xref:fundamentals/index#content-root)路徑 `TEntryPoint` `System.Reflection.Assembly.FullName` 。 如果找不到具有正確索引鍵的屬性，則會 `WebApplicationFactory` 切換回以搜尋方案檔 (*.Sln*) 並將 `TEntryPoint` 元件名稱附加至方案目錄。 應用程式根目錄 (內容根路徑) 用來探索視圖和內容檔案。

## <a name="disable-shadow-copying"></a>停用陰影複製

陰影複製會導致測試在和輸出目錄不同的目錄中執行。 若要讓測試正常運作，必須停用陰影複製。 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)會使用 xUnit 並停用 xUnit 的陰影複製，方法是在具有正確設定的檔案*上包含xunit.runner.js* 。 如需詳細資訊，請參閱 [使用 JSON 設定 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。

使用下列內容，將檔案 * 上的xunit.runner.js* 新增至測試專案的根目錄：

```json
{
  "shadowCopy": false
}
```

如果使用 Visual Studio，請將檔案的 [ **複製到輸出目錄** ] 屬性設定為 [ **永遠複製**]。 如果未使用 Visual Studio，請將 `Content` 目標新增至測試應用程式的專案檔：

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>物件的處置

`IClassFixture`執行實施測試之後，當 xUnit 處置[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)時，會處置[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。 如果開發人員所具現化的物件需要處置，請在執行時處置它們 `IClassFixture` 。 如需詳細資訊，請參閱 [執行 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。

## <a name="integration-tests-sample"></a>整合測試範例

[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)是由兩個應用程式所組成：

| 應用程式 | 專案目錄 | 描述 |
| --- | ----------------- | ----------- |
|  (在 SUT) 的訊息應用程式 | *src/ Razor PagesProject* | 允許使用者新增、刪除、刪除所有訊息，以及分析訊息。 |
| 測試應用程式 | *測試/ Razor PagesProject* | 用於整合測試的 SUT。 |

您可以使用 IDE 的內建測試功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）來執行測試。 如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令列，請在 [ *測試/ Razor PagesProject* ] 目錄中的命令提示字元執行下列命令：

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a> (的 SUT) 組織的訊息應用程式

SUT 是 Razor 具有下列特性的頁面訊息系統：

* 應用程式的 [索引] 頁面 (*pages/index. cshtml* 和 *pages/index. CSHTML*) 提供 UI 和頁面模型方法，可控制訊息的新增、刪除和分析 (每個訊息) 的平均單字。
* 訊息是由 `Message` 類別 (*Data/message .cs*) 所描述，其中包含兩個屬性： `Id` (索引鍵) 和 `Text` (訊息) 。 `Text`屬性是必要的，且限制為200個字元。
* 訊息是使用 [Entity Framework 的記憶體內部資料庫](/ef/core/providers/in-memory/)&#8224; 來儲存。
* 應用程式在其資料庫內容類別中包含 (DAL) 的資料存取層， `AppDbContext` (*Data/AppDbCoNtext .cs*) 。
* 如果應用程式啟動時資料庫是空的，則會使用三個訊息來初始化訊息存放區。
* 應用程式包含 `/SecurePage` 只能由已驗證的使用者存取的。

&#8224;EF 主題、 [使用 InMemory 進行測試](/ef/core/miscellaneous/testing/in-memory)，說明如何使用記憶體內部資料庫來搭配 MSTest 進行測試。 本主題使用 [xUnit](https://xunit.github.io/) 測試架構。 跨不同測試架構的測試概念和測試的執行方式類似，但不完全相同。

雖然應用程式不會使用存放庫模式，而不是 [工作單元)  (](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效範例，但 Razor 頁面支援這些開發模式。 如需詳細資訊，請參閱 [設計基礎結構持續性層](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 和 [測試控制器邏輯](/aspnet/core/mvc/controllers/testing) (範例會) 執行儲存機制模式。

### <a name="test-app-organization"></a>測試應用程式組織

測試應用程式是 [ *測試/ Razor PagesProject* ] 目錄中的主控台應用程式。

| 測試應用程式目錄 | 描述 |
| ------------------ | ----------- |
| *AuthTests* | 包含的測試方法：<ul><li>未經驗證的使用者存取安全的頁面。</li><li>使用模擬以已驗證的使用者存取安全的頁面 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> 。</li><li>取得 GitHub 使用者設定檔，並檢查設定檔的使用者登入。</li></ul> |
| *BasicTests* | 包含路由和內容類型的測試方法。 |
| *IntegrationTests* | 包含使用自訂類別之 [索引] 頁面的整合測試 `WebApplicationFactory` 。 |
| *協助程式/公用程式* | <ul><li>*Utilities.cs* 包含 `InitializeDbForTests` 用來將測試資料植入資料庫的方法。</li><li>*HtmlHelpers.cs* 會提供方法，以傳回 `IHtmlDocument` 測試方法所使用的 AngleSharp。</li><li>*HttpClientExtensions.cs* 會提供的多載， `SendAsync` 以將要求提交至 SUT。</li></ul> |

測試架構為 [xUnit](https://xunit.github.io/)。 整合測試是使用 [AspNetCore TestHost](/dotnet/api/microsoft.aspnetcore.testhost)，其中包含 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。 由於使用 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 封裝來設定測試主機和測試伺服器，因此 `TestHost` 和套件不需要測試應用程式的專案檔 `TestServer` 或開發人員在測試應用程式中進行直接封裝參考。

**植入資料庫以進行測試**

在測試執行之前，整合測試通常需要資料庫中的小型資料集。 例如，刪除測試會呼叫資料庫記錄刪除，因此資料庫必須至少有一筆記錄，刪除要求才能成功。

範例應用程式會使用 *Utilities.cs* 中的三個訊息來植入資料庫，測試可在執行時使用這些訊息：

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>其他資源

* [單元測試](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
