---
title: 從 ASP.NET Web API 遷移至 ASP.NET Core
author: ardalis
description: 瞭解如何將 web API 的執行從 ASP.NET 4.x Web API 遷移至 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 3bd34f677271ddfc00e6e65ddf3a5e3c10eec863
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588779"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>從 ASP.NET Web API 遷移至 ASP.NET Core

[Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)

ASP.NET 4.x Web API 是一種 HTTP 服務，可達到各式各樣的用戶端，包括瀏覽器和行動裝置。 ASP.NET Core 將 ASP.NET 4.x 的 MVC 和 Web API 應用程式模型合併為單一程式設計模型，稱為 ASP.NET Core MVC。 本文示範從 ASP.NET 4.x Web API 遷移至 ASP.NET Core MVC 所需的步驟。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>必要條件

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>複習 ASP.NET 4.x Web API 專案

本文使用 [ASP.NET WEB API 2 入門](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中建立的 *ProductsApp* 專案。 在該專案中，基本的 ASP.NET 4.x Web API 專案會依照下列方式設定。

在 *Global.asax.cs* 中，呼叫會進行 `WebApiConfig.Register` 下列動作：

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`類別可在 *App_Start* 資料夾中找到，並具有靜態 `Register` 方法：

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

上述類別：

* 設定 [屬性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，但實際上不會使用它。
* 設定路由表。
範例程式碼會預期 Url 必須符合格式 `/api/{controller}/{id}` ，而且 `{id}` 是選擇性的。

下列各節示範如何將 Web API 專案遷移至 ASP.NET Core MVC。

## <a name="create-the-destination-project"></a>建立目的地專案

在 Visual Studio 中建立新的空白方案，並新增 ASP.NET 4.x Web API 專案以進行遷移：

1. 從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。
1. 選取 **空白的解決方案** 範本，然後選取 **[下一步]**。
1. 將方案命名為 *WebAPIMigration*。 選取 [建立]  。
1. 將現有的 *ProductsApp* 專案加入至方案。

新增要遷移至的 API 專案：

1. 將新的 **ASP.NET Core Web 應用程式** 專案新增至方案。
1. 在 [ **設定您的新專案** ] 對話方塊中，將專案命名為 *ProductsCore*，然後選取 [ **建立**]。
1. 在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。 選取 [API] 專案範本，然後選取 [確定]。
1. 從新的 *ProductsCore* 專案中移除 *WeatherForecast.cs* 和 *控制器/WeatherForecastController .cs* 範例檔案。

方案現在包含兩個專案。 下列各節說明如何將 *ProductsApp* 專案的內容遷移至 *ProductsCore* 專案。

## <a name="migrate-configuration"></a>移轉組態

ASP.NET Core 不會使用 *App_Start* 資料夾或 *global.asax* 檔案。 此外，也會在發行時新增 *web.config* 檔案。

`Startup` 類別：

* 取代 *global.asax*。
* 處理所有應用程式啟動工作。

如需詳細資訊，請參閱<xref:fundamentals/startup>。

## <a name="migrate-models-and-controllers"></a>遷移模型和控制器

下列程式碼顯示 `ProductsController` 要針對 ASP.NET Core 更新的：

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

更新 `ProductsController` ASP.NET Core 的：

1. 將 *控制器/productscontroller.cs* 和 *模型* 資料夾從原始專案複製到新專案。
1. 將複製的檔案根命名空間變更為 `ProductsCore` 。
1. 將 `using ProductsApp.Models;` 語句更新為 `using ProductsCore.Models;` 。

下列元件不存在於 ASP.NET Core 中：

* `ApiController` 類別
* `System.Web.Http` 命名空間
* `IHttpActionResult` 介面

進行下列變更：

1. 將 `ApiController` 變更為 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。 加入 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 參考。
1. 刪除 `using System.Web.Http;`。
1. 將 `GetProduct` 動作的傳回類型從變更 `IHttpActionResult` 為 `ActionResult<Product>` 。
1. 簡化 `GetProduct` 動作的 `return` 語句，如下所示：

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>設定路由

ASP.NET Core *API* 專案範本在產生的程式碼中包含端點路由設定。

下列 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> 內容會 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 呼叫：

* 在 [中介軟體](xref:fundamentals/middleware/index) 管線中註冊路由對應和端點執行。
* 取代 *ProductsApp* 專案的 *App_Start/webapiconfig.cs* 檔。

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

設定路由，如下所示：

1. `ProductsController`以下列屬性標示類別：

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    上述屬性會設定 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 控制器的屬性路由模式。 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)屬性會讓屬性路由成為此控制器中所有動作的需求。

    屬性路由支援權杖，例如 `[controller]` 和 `[action]` 。 在執行時間，每個權杖都會分別取代為已套用屬性的控制器或動作名稱。 標記：
    * 減少專案中魔術字串的數目。
    * 套用自動重新命名重構時，請確定路由與對應的控制器和動作保持同步。
1. 啟用動作的 HTTP Get 要求 `ProductController` ：
    * 將 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 屬性套用至 `GetAllProducts` 動作。
    * 將 `[HttpGet("{id}")]` 屬性套用至 `GetProduct` 動作。

執行已遷移的專案，然後流覽至 `/api/products` 。 三個產品的完整清單隨即出現。 瀏覽至 `/api/products/1`。 第一個產品隨即出現。

## <a name="additional-resources"></a>其他資源

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>必要條件

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>複習 ASP.NET 4.x Web API 專案

本文使用 [ASP.NET WEB API 2 入門](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中建立的 *ProductsApp* 專案。 在該專案中，基本的 ASP.NET 4.x Web API 專案會依照下列方式設定。

在 *Global.asax.cs* 中，呼叫會進行 `WebApiConfig.Register` 下列動作：

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`類別可在 *App_Start* 資料夾中找到，並具有靜態 `Register` 方法：

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

這個類別會設定 [屬性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，雖然它實際上不是在專案中使用。 它也會設定 ASP.NET Web API 所使用的路由表。 在此情況下，ASP.NET 4.x Web API 預期 Url 必須符合格式 `/api/{controller}/{id}` ，而且 `{id}` 是選擇性的。

下列各節示範如何將 Web API 專案遷移至 ASP.NET Core MVC。

## <a name="create-the-destination-project"></a>建立目的地專案

在 Visual Studio 中完成下列步驟：

* 移至 **[**  >  **新增**  >  **專案**]  >  **其他專案類型**  >  **Visual Studio 方案**。 選取 [ **空白方案**]，並將方案命名為 *WebAPIMigration*。 按一下 [確定] 按鈕。
* 將現有的 *ProductsApp* 專案加入至方案。
* 將新的 **ASP.NET Core Web 應用程式** 專案新增至方案。 從下拉式清單中選取 [ **.Net Core** 目標 framework]，然後選取 [ **API** ] 專案範本。 將專案命名為 *ProductsCore*，然後按一下 [ **確定]** 按鈕。

方案現在包含兩個專案。 下列各節說明如何將 *ProductsApp* 專案的內容遷移至 *ProductsCore* 專案。

## <a name="migrate-configuration"></a>移轉組態

ASP.NET Core 不會使用：

* *App_Start* 資料夾或 *global.asax* 檔案
* *web.config* 檔案會在發行時新增。

`Startup` 類別：

* 取代 *global.asax*。
* 處理所有應用程式啟動工作。

如需詳細資訊，請參閱<xref:fundamentals/startup>。

在 ASP.NET Core MVC 中，當在中呼叫時，預設會包含屬性路由 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` 。 下列 `UseMvc` 呼叫會取代 *ProductsApp* 專案的 *App_Start/webapiconfig.cs* 檔：

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>遷移模型和控制器

下列程式碼顯示 `ProductsController` ASP.NET Core 的更新： [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

更新 `ProductsController` ASP.NET Core 的：

1. 將 *控制器/productscontroller.cs* 從原始專案複製到新專案。
1. 從原始專案將 [ *模型* ] 資料夾複製到新專案。
1. 將複製的檔案根命名空間變更為 `ProductsCore` 。
1. 將 `using ProductsApp.Models;` 語句更新為 `using ProductsCore.Models;` 。

下列元件不存在於 ASP.NET Core 中：

* `ApiController` 類別
* `System.Web.Http` 命名空間
* `IHttpActionResult` 介面

進行下列變更：

1. 將 `ApiController` 變更為 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。 加入 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 參考。
1. 刪除 `using System.Web.Http;`。
1. 將 `GetProduct` 動作的傳回類型從變更 `IHttpActionResult` 為 `ActionResult<Product>` 。
1. 簡化 `GetProduct` 動作的 `return` 語句，如下所示：

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>設定路由

設定路由，如下所示：

1. `ProductsController`以下列屬性標示類別：

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    上述屬性會設定 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 控制器的屬性路由模式。 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)屬性會讓屬性路由成為此控制器中所有動作的需求。

    屬性路由支援權杖，例如 `[controller]` 和 `[action]` 。 在執行時間，每個權杖都會分別取代為已套用屬性的控制器或動作名稱。 這些標記會減少專案中魔術字串的數目。 標記也可確保在套用自動重新命名重構時，路由會與對應的控制器和動作保持同步。
1. 將專案的相容性模式設定為 ASP.NET Core 2.2：

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    上述變更：

    * 必須使用 `[ApiController]` 控制器層級的屬性。
    * 選擇 ASP.NET Core 2.2 中引進的潛在中斷行為。
1. 啟用動作的 HTTP Get 要求 `ProductController` ：
    * 將 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 屬性套用至 `GetAllProducts` 動作。
    * 將 `[HttpGet("{id}")]` 屬性套用至 `GetProduct` 動作。

執行已遷移的專案，然後流覽至 `/api/products` 。 三個產品的完整清單隨即出現。 瀏覽至 `/api/products/1`。 第一個產品隨即出現。

## <a name="compatibility-shim"></a>相容性填充碼

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)程式庫提供相容性填充碼，可將 ASP.NET 4.X Web API 專案移至 ASP.NET Core。 相容性填充碼會擴充 ASP.NET 核心，以支援 ASP.NET 4.x Web API 2 中的一些慣例。 本檔先前移植的範例已足夠基本，因此不需要相容性填充碼。 針對較大型的專案，使用相容性填充碼有助於暫時橋接 ASP.NET Core 與 ASP.NET 4.x Web API 2 之間的 API 差距。

Web API 相容性填充碼旨在作為暫時量值，以支援將大型 ASP.NET 4.x Web API 專案遷移至 ASP.NET Core。 經過一段時間，專案應該更新為使用 ASP.NET 核心模式，而不是依賴相容性填充碼。

包含的相容性功能 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 包括：

* 加入 `ApiController` 類型，因此不需要更新控制器的基底類型。
* 啟用 Web API 樣式的模型系結。 根據預設，ASP.NET Core MVC 模型系結函式類似于 ASP.NET 4.x MVC 5 的功能。 相容性填充碼會將模型系結變更為更類似 ASP.NET 4.x Web API 2 模型系結慣例。 例如，複雜型別會自動從要求主體系結。
* 擴充模型系結，讓控制器動作可以採用型別的參數 `HttpRequestMessage` 。
* 加入訊息格式器，允許動作傳回型別的結果 `HttpResponseMessage` 。
* 新增 Web API 2 動作可能用來提供回應的額外回應方法：
  * `HttpResponseMessage` 發電機：
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * 動作結果方法：
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* 將的實例加入 `IContentNegotiator` 至應用程式的相依性插入 (DI) 容器，並從 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)提供與內容協商相關的類型。 這類類型的範例包括 `DefaultContentNegotiator` 和 `MediaTypeFormatter` 。

使用相容性填充碼：

1. 安裝 [AspNetCore >webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 套件。
1. 藉由呼叫，向應用程式的 DI 容器註冊相容性填充碼的服務 `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` 。
1. 使用 `MapWebApiRoute` `IRouteBuilder` 應用程式呼叫中的來定義 web API 特定路由 `IApplicationBuilder.UseMvc` 。

## <a name="additional-resources"></a>其他資源

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
