---
title: 教學課程：使用 ASP.NET Core 建立 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 建置 Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564087"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>教學課程：使用 ASP.NET Core 建立 Web API

由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)和 [Mike Wasson](https://github.com/mikewasson)

本教學課程將教導您使用 ASP.NET Core 建立 Web API 的基本概念。

::: moniker range=">= aspnetcore-5.0"

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Web API 專案。
> * 新增模型類別和資料庫內容。
> * 使用 CRUD 方法 Scaffold 控制器。
> * 設定路由、URL 路徑和傳回值。
> * 使用 Postman 呼叫 Web API。

結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。

## <a name="overview"></a>概觀

本教學課程會建立以下 API：

|API | 描述 | 要求本文 | 回應本文 |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | 取得所有待辦事項 | 無 | 待辦事項的陣列|
|`GET /api/TodoItems/{id}` | 依識別碼取得項目 | 無 | 待辦事項|
|`POST /api/TodoItems` | 新增記錄 | 待辦事項 | 待辦事項 |
|`PUT /api/TodoItems/{id}` | 更新現有的項目 &nbsp; | 待辦事項 | 無 |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | 刪除專案 &nbsp;&nbsp; | 無 | 無|

下圖顯示應用程式的設計。

![左側方塊代表用戶端。 它會送出要求並接收來自應用程式 (右側繪製的方塊) 的回應。 在應用程式方塊中，三個方塊代表控制器、模型以及資料存取層。 要求進入應用程式的控制器，而在控制器與資料存取層之間進行讀取/寫入作業。 模型會序列化並在回應中傳回至用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a>建立 Web 專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。
* 選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。
* 將專案命名為 *TodoApi*，然後按一下 [建立]。
* 在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 5.0** ]。 選取 **API** 範本，然後按一下 [建立]。

![VS 新增專案對話方塊](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 將目錄 (`cd`) 變更為包含專案資料夾的資料夾。
* 執行下列命令：

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* 當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。

  上述命令：

  * 建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。
  * 新增下一節需要的 NuGet 套件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* 在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。 在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* 在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 5.X **目標 Framework**。 選取 [下一步] 。

* 針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

在專案資料夾中開啟命令終端機，然後執行下列命令：

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a>測試專案

專案範本會建立 `WeatherForecast` 支援 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)的 API。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

按 Ctrl+F5 即可執行而不使用偵錯工具。

[!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio 啟動：

* IIS Express web 伺服器。
* 預設瀏覽器並流覽至 `https://localhost:<port>/swagger/index.html` ，其中 `<port>` 是隨機播放的埠號碼。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

按 Ctrl+F5 執行應用程式。 在瀏覽器中，移至下列 URL： [https://localhost:5001/swagger](https://localhost:5001/swagger)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。 Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。 傳回 HTTP 404 (找不到) 錯誤。 將 `/swagger` 附加至 URL (將 URL 變更為 `https://localhost:<port>/swagger`)。

---

Swagger 頁面隨即 `/swagger/index.html` 顯示。 選取 [**立即**  >  **試用**]  >  **執行**。 頁面會顯示：

* 用來測試 WeatherForecast API 的 [捲曲](https://curl.haxx.se/) 命令。
* 用來測試 WeatherForecast API 的 URL。
* 回應碼、主體和標頭。
* 具有媒體類型的下拉式清單方塊，以及範例值和架構。

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
Swagger 可用來產生 web Api 的實用檔和說明頁面。 本教學課程著重于建立 web API。 如需 Swagger 的詳細資訊，請參閱 <xref:tutorials/web-api-help-pages-using-swagger> 。

將 **要求 URL** 複製並貼入瀏覽器中：  `https://localhost:<port>/WeatherForecast`

系統會傳回與下列類似的 JSON：

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a>更新 launchUrl

在 *Properties\launchSettings.js開啟*，請 `launchUrl` 從更新 `"swagger"` 為 `"api/TodoItems"` ：

```json
"launchUrl": "api/TodoItems",
```

由於 Swagger 已移除，因此上述標記會將啟動的 URL 變更為在下列各節中新增之控制器的 GET 方法。

## <a name="add-a-model-class"></a>新增模型類別

「模型」是代表應用程式所管理資料的一組類別。 此應用程式的模型是單一 `TodoItem` 類別。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **方案總管** 中，以滑鼠右鍵按一下專案。 選取 **[**  >  **新增資料夾**]。 為資料夾命名 *Models* 。

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。 將類別命名為 *TodoItem*，然後選取 [新增]。

* 以下列程式碼取代範本程式碼：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為的資料夾 *Models* 。

* `TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 以滑鼠右鍵按一下專案。 選取 **[**  >  **新增資料夾**]。 為資料夾命名 *Models* 。

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。

* 將類別命名為 *TodoItem*，然後按一下 [新增]。

* 以下列程式碼取代範本程式碼：

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。

模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。

## <a name="add-a-database-context"></a>新增資料庫內容

「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。 此類別是透過衍生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 類別來建立。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>新增 NuGet 套件

* 在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。
* 選取 [**流覽**] 索引標籤，然後在 [搜尋] 方塊中輸入 **microsoft.entityframeworkcore。**
* 選取左窗格中的 [ **microsoft.entityframeworkcore** ]。
* 選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。

![NuGet 套件管理員](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a>新增 TodoCoNtext 資料庫內容

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。 將類別命名為 *TodoContext*，然後按一下 [新增]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 將 `TodoContext` 類別新增至 *Models* 資料夾。

---

* 輸入下列程式碼：

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>登錄資料庫內容

在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。 此容器會將服務提供給控制器。

使用下列程式碼更新 *Startup.cs* ：

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

上述程式碼：

* 移除 Swagger 呼叫。
* 移除未使用的 `using` 宣告。
* 將資料庫內容新增至 DI 容器。
* 指定資料庫內容將會使用記憶體內部資料庫。

## <a name="scaffold-a-controller"></a>Scaffold 控制器

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以滑鼠右鍵按一下 *Controllers* 資料夾。
* 選取 [新增]**[新增 Scaffold 項目]** > 。
* 選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。
* 在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：

  * 選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**
  * 選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**
  * 選取 [新增]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

執行下列命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

上述命令：

* 新增 Scaffolding 所需的 NuGet 套件。
* 安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。
* Scaffold `TodoItemsController`。

---

產生的程式碼：

* 標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。 這個屬性表示控制器會回應 Web API 要求。 如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。
* 使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。 控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。

的 ASP.NET Core 範本：

* 具有 views 的控制器包含 `[action]` 在路由範本中。
* API 控制器不包含 `[action]` 在路由範本中。

當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。 也就是，不會在相符的路由中使用動作的相關聯方法名稱。

## <a name="update-the-posttodoitem-create-method"></a>更新 PostTodoItem create 方法

更新中的 return 語句 `PostTodoItem` ，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。 該方法會從 HTTP 要求本文取得待辦事項的值。

如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：

* 如果成功，則傳回 [HTTP 201 狀態碼](https://developer.mozilla.org/docs/Web/HTTP/Status/201) 。 對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。
* 將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。 `Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。 如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。
* 參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。 C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。

### <a name="install-postman"></a>安裝 Postman

本教學課程使用 Postman 來測試 Web API。

* 安裝 [Postman](https://www.getpostman.com/downloads/)
* 啟動 Web 應用程式。
* 啟動 Postman。
* 停用 [SSL certificate verification] \(SSL 憑證驗證\)
  * 從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。
    > [!WARNING]
    > 在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>使用 Postman 測試 PostTodoItem

* 建立新的要求。
* 將 HTTP 方法設為 `POST`。
* 將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。 例如： `https://localhost:5001/api/TodoItems` 。
* 選取 [Body] \(本文\) 索引標籤。
* 選取 [原始] 選項按鈕。
* 將類型設定為 **JSON (application/json)**。
* 在要求本文中，針對待辦項目輸入 JSON：

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* 選取 [傳送]。

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>測試位置標頭 URI

您可以在瀏覽器中測試位置標頭 URI。 複製位置標頭 URI，並將其貼到瀏覽器中。

若要在 Postman 中進行測試：

* 在 [回應] 窗格中選取 [標頭] 索引標籤。
* 複製 [位置] 標頭值：

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* 將 HTTP 方法設為 `GET`。
* 將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。 例如： `https://localhost:5001/api/TodoItems/1` 。
* 選取 [傳送]。

## <a name="examine-the-get-methods"></a>檢查 GET 方法

系統會執行兩個 GET 端點：

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。 例如：

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

`GetTodoItems` 的呼叫會產生類似下列的回應：

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>使用 Postman 測試 Get

* 建立新的要求。
* 將 HTTP 方法設定為 **GET**。
* 將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。 例如： `https://localhost:5001/api/TodoItems` 。
* 在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。
* 選取 [傳送]。

這個應用程式會使用記憶體內部資料庫。 如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。 如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。

## <a name="routing-and-url-paths"></a>傳送和 URL 路徑

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。 每個方法的 URL 路徑的建構方式如下：

* 一開始在控制器的 `Route` 屬性中使用範本字串：

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* 以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。 在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。 ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。
* 如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。 此範例不使用範本。 如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。 當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>傳回值

和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。 ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。 如果沒有任何未處理的例外狀況，則此傳回型別的回應碼為 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)。 未處理的例外狀況會轉譯成 5xx 錯誤。

`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。 例如，`GetTodoItem` 可傳回兩個不同的狀態值：

* 如果沒有任何專案符合要求的識別碼，方法會傳回 [404 狀態](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。
* 否則，方法會傳回 200 與 JSON 回應本文。 傳回 `item` 會導致 HTTP 200 回應。

## <a name="the-puttodoitem-method"></a>PutTodoItem 方法

檢查 `PutTodoItem` 方法：

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。 回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。 若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。

如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。

### <a name="test-the-puttodoitem-method"></a>測試 PutTodoItem 方法

這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。 資料庫中必須有項目，您才能進行 PUT 呼叫。 在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。

更新識別碼為1的待辦事項，並將其名稱設定為 `"feed fish"` ：

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

下圖顯示 Postman 更新：

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem 方法

檢查 `DeleteTodoItem` 方法：

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>測試 DeleteTodoItem 方法

使用 Postman 刪除待辦事項：

* 將方法設定為 `DELETE`。
* 設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。
* 選取 [傳送]。

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a>防止過度張貼

範例應用程式目前會公開整個 `TodoItem` 物件。 生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。 這項功能有許多原因，而且安全性是主要的原因。 模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。 此文章中使用 **DTO** 。

DTO 可以用來：

* 防止過度張貼。
* 隱藏用戶端不應該看到的屬性。
* 略過某些屬性，以減少承載大小。
* 壓平合併包含嵌套物件的物件圖形。 簡維物件圖形對於用戶端來說可能更方便。

若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。

確認您可以張貼並取得秘密欄位。

建立 DTO 模型：

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

更新 `TodoItemsController` 以使用 `TodoItemDTO` ：

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

確認您無法張貼或取得秘密欄位。

## <a name="call-the-web-api-with-javascript"></a>使用 JavaScript 呼叫 Web API

請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Web API 專案。
> * 新增模型類別和資料庫內容。
> * 使用 CRUD 方法 Scaffold 控制器。
> * 設定路由、URL 路徑和傳回值。
> * 使用 Postman 呼叫 Web API。

結束時，您會有一個 Web API，可以管理儲存在資料庫中的「待辦事項」。

## <a name="overview"></a>概觀

本教學課程會建立以下 API：

|API | 描述 | 要求本文 | 回應本文 |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | 取得所有待辦事項 | 無 | 待辦事項的陣列|
|`GET /api/TodoItems/{id}` | 依識別碼取得項目 | 無 | 待辦事項|
|`POST /api/TodoItems` | 新增記錄 | 待辦事項 | 待辦事項 |
|`PUT /api/TodoItems/{id}` | 更新現有的項目 &nbsp; | 待辦事項 | 無 |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | 刪除專案 &nbsp;&nbsp; | 無 | 無|

下圖顯示應用程式的設計。

![左側方塊代表用戶端。 它會送出要求並接收來自應用程式 (右側繪製的方塊) 的回應。 在應用程式方塊中，三個方塊代表控制器、模型以及資料存取層。 要求進入應用程式的控制器，而在控制器與資料存取層之間進行讀取/寫入作業。 模型會序列化並在回應中傳回至用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>建立 Web 專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。
* 選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。
* 將專案命名為 *TodoApi*，然後按一下 [建立]。
* 在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。 選取 **API** 範本，然後按一下 [建立]。

![VS 新增專案對話方塊](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 將目錄 (`cd`) 變更為包含專案資料夾的資料夾。
* 執行下列命令：

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* 當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。

  上述命令：

  * 建立新的 Web API 專案，然後在 Visual Studio Code 中予以開啟。
  * 新增下一節需要的 NuGet 套件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* 在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。 在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。

  ![macOS API 範本選取專案](first-web-api-mac/_static/api_template.png)

* 在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 3.X **目標 Framework**。 選取 [下一步] 。

* 針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。

  ![設定對話方塊](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

在專案資料夾中開啟命令終端機，然後執行下列命令：

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>測試 API

專案範本會建立 `WeatherForecast` API。 從瀏覽器呼叫 `Get` 方法來測試應用程式。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

按 Ctrl+F5 執行應用程式。 Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是隨機選擇的通訊埠編號。

如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。 在接著出現的 [安全性警告] 對話方塊中，選取 [是]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按 Ctrl+F5 執行應用程式。 在瀏覽器中，前往下列 URL：`https://localhost:5001/WeatherForecast`。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。 Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。 傳回 HTTP 404 (找不到) 錯誤。 將 `/WeatherForecast` 附加至 URL (將 URL 變更為 `https://localhost:<port>/WeatherForecast`)。

---

系統會傳回與下列類似的 JSON：

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>新增模型類別

「模型」是代表應用程式所管理資料的一組類別。 此應用程式的模型是單一 `TodoItem` 類別。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **方案總管** 中，以滑鼠右鍵按一下專案。 選取 **[**  >  **新增資料夾**]。 為資料夾命名 *Models* 。

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。 將類別命名為 *TodoItem*，然後選取 [新增]。

* 使用下列程式碼取代範本程式碼：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為的資料夾 *Models* 。

* `TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 以滑鼠右鍵按一下專案。 選取 **[**  >  **新增資料夾**]。 為資料夾命名 *Models* 。

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。

* 將類別命名為 *TodoItem*，然後按一下 [新增]。

* 使用下列程式碼取代範本程式碼：

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。

模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。

## <a name="add-a-database-context"></a>新增資料庫內容

「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。 此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>新增 NuGet 套件

* 在 [工具] 功能表上，選取 [NuGet 套件管理員] > [管理解決方案的 NuGet 套件]。
* 選取 [**流覽**] 索引標籤，然後在 [搜尋] 方塊中輸入 **microsoft.entityframeworkcore。**
* 選取左窗格中的 [ **microsoft.entityframeworkcore** ]。
* 選取右窗格中的 [專案] 核取方塊，然後選取 [安裝]。

![NuGet 套件管理員](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>新增 TodoCoNtext 資料庫內容

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。 將類別命名為 *TodoContext*，然後按一下 [新增]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 將 `TodoContext` 類別新增至 *Models* 資料夾。

---

* 輸入下列程式碼：

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>登錄資料庫內容

在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。 此容器會將服務提供給控制器。

使用下列醒目提示的程式碼更新 *Startup.cs*：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

上述程式碼：

* 移除未使用的 `using` 宣告。
* 將資料庫內容新增至 DI 容器。
* 指定資料庫內容將會使用記憶體內部資料庫。

## <a name="scaffold-a-controller"></a>Scaffold 控制器

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以滑鼠右鍵按一下 *Controllers* 資料夾。
* 選取 [新增]**[新增 Scaffold 項目]** > 。
* 選取 [使用 Entity Framework 執行動作的 API 控制器]，然後選取 [新增]。
* 在 [使用 Entity Framework 執行動作的 API 控制器] 對話方塊中：

  * 選取 **模型類別** 中的 [ **TodoItem (TodoApi] Models) 。**
  * 選取 **資料內容類別** 中的 **TodoCoNtext (TodoApi Models) 。**
  * 選取 [新增]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

執行下列命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

上述命令：

* 新增 Scaffolding 所需的 NuGet 套件。
* 安裝 Scaffolding 引擎 (`dotnet-aspnet-codegenerator`)。
* Scaffold `TodoItemsController`。

---

產生的程式碼：

* 標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。 這個屬性表示控制器會回應 Web API 要求。 如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。
* 使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。 控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。

的 ASP.NET Core 範本：

* 具有 views 的控制器包含 `[action]` 在路由範本中。
* API 控制器不包含 `[action]` 在路由範本中。

當 `[action]` 權杖不在路由範本中時，會從路由中排除 [動作](xref:mvc/controllers/routing#action) 名稱。 也就是，不會在相符的路由中使用動作的相關聯方法名稱。

## <a name="examine-the-posttodoitem-create-method"></a>檢查 PostTodoItem 建立方法

取代 `PostTodoItem` 中的 return 陳述式，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 運算子：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。 該方法會從 HTTP 要求本文取得待辦事項的值。

如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：

* 成功時會傳回 HTTP 201 狀態碼。 對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。
* 將 [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 標頭新增至回應。 `Location`標頭會指定新建立之待辦事項的[URI](https://developer.mozilla.org/docs/Glossary/URI) 。 如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。
* 參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。 C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。

### <a name="install-postman"></a>安裝 Postman

本教學課程使用 Postman 來測試 Web API。

* 安裝 [Postman](https://www.getpostman.com/downloads/)
* 啟動 Web 應用程式。
* 啟動 Postman。
* 停用 [SSL certificate verification] \(SSL 憑證驗證\)
  * 從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。
    > [!WARNING]
    > 在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>使用 Postman 測試 PostTodoItem

* 建立新的要求。
* 將 HTTP 方法設為 `POST`。
* 將 URI 設定為 `https://localhost:<port>/api/TodoItems` 。 例如： `https://localhost:5001/api/TodoItems` 。
* 選取 [Body] \(本文\) 索引標籤。
* 選取 [原始] 選項按鈕。
* 將類型設定為 **JSON (application/json)**。
* 在要求本文中，針對待辦項目輸入 JSON：

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* 選取 [傳送]。

  ![Postman 與建立要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a>使用 Postman 測試 location 標頭 URI

* 在 [回應] 窗格中選取 [標頭] 索引標籤。
* 複製 [位置] 標頭值：

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/3/create.png)

* 將 HTTP 方法設為 `GET`。
* 將 URI 設定為 `https://localhost:<port>/api/TodoItems/1` 。 例如： `https://localhost:5001/api/TodoItems/1` 。
* 選取 [傳送]。

## <a name="examine-the-get-methods"></a>檢查 GET 方法

這些方法會實作兩個 GET 端點：

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

從瀏覽器或 Postman 呼叫這兩個端點來測試應用程式。 例如：

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

`GetTodoItems` 的呼叫會產生類似下列的回應：

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>使用 Postman 測試 Get

* 建立新的要求。
* 將 HTTP 方法設定為 **GET**。
* 將要求 URI 設定為 `https://localhost:<port>/api/TodoItems` 。 例如： `https://localhost:5001/api/TodoItems` 。
* 在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。
* 選取 [傳送]。

這個應用程式會使用記憶體內部資料庫。 如果應用程式在停止後再啟動，上述 GET 要求將不會傳回任何資料。 如果沒有傳回任何資料，請將資料 [POST](#post) 到應用程式。

## <a name="routing-and-url-paths"></a>傳送和 URL 路徑

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。 每個方法的 URL 路徑的建構方式如下：

* 一開始在控制器的 `Route` 屬性中使用範本字串：

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* 以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。 在此範例中，控制器類別名稱是 **TodoItems** Controller，因此控制器名稱是 "TodoItems"。 ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。
* 如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。 此範例不使用範本。 如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。 當叫 `GetTodoItem` 用時，會將 `"{id}"` URL 中的值提供給方法的 `id` 參數。

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>傳回值 

和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。 ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。 此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。 未處理的例外狀況會轉譯成 5xx 錯誤。

`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。 例如，`GetTodoItem` 可傳回兩個不同的狀態值：

* 如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。
* 否則，方法會傳回 200 與 JSON 回應本文。 傳回 `item` 會導致 HTTP 200 回應。

## <a name="the-puttodoitem-method"></a>PutTodoItem 方法

檢查 `PutTodoItem` 方法：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。 回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。 若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。

如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。

### <a name="test-the-puttodoitem-method"></a>測試 PutTodoItem 方法

這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。 資料庫中必須有項目，您才能進行 PUT 呼叫。 在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。

更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

下圖顯示 Postman 更新：

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem 方法

檢查 `DeleteTodoItem` 方法：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>測試 DeleteTodoItem 方法

使用 Postman 刪除待辦事項：

* 將方法設定為 `DELETE`。
* 設定要刪除之物件的 URI (例如 `https://localhost:5001/api/TodoItems/1`) 。
* 選取 [傳送]。

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a>防止過度張貼

範例應用程式目前會公開整個 `TodoItem` 物件。 生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。 這項功能有許多原因，而且安全性是主要的原因。 模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。 此文章中使用 **DTO** 。

DTO 可以用來：

* 防止過度張貼。
* 隱藏用戶端不應該看到的屬性。
* 略過某些屬性，以減少承載大小。
* 壓平合併包含嵌套物件的物件圖形。 簡維物件圖形對於用戶端來說可能更方便。

若要示範 DTO 方法，請更新 `TodoItem` 類別以包含秘密欄位：

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

此應用程式必須隱藏秘密欄位，但系統管理應用程式可以選擇將它公開。

確認您可以張貼並取得秘密欄位。

建立 DTO 模型：

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

更新 `TodoItemsController` 以使用 `TodoItemDTO` ：

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

確認您無法張貼或取得秘密欄位。

## <a name="call-the-web-api-with-javascript"></a>使用 JavaScript 呼叫 Web API

請參閱 [教學課程：使用 JavaScript 呼叫 ASP.NET Core WEB API](xref:tutorials/web-api-javascript)。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Web API 專案。
> * 新增模型類別和資料庫內容。
> * 新增控制器。
> * 新增 CRUD 方法。
> * 設定路由和 URL 路徑。
> * 指定傳回值。
> * 使用 Postman 呼叫 Web API。
> * 使用 JavaScript 呼叫 Web API。

結束時，您將會有一個可管理關聯式資料庫中所儲存「待辦事項」的 Web API。

## <a name="overview-21"></a>總覽2。1

本教學課程會建立以下 API：

|API | 描述 | 要求本文 | 回應本文 |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | 取得所有待辦事項 | 無 | 待辦事項的陣列|
|GET /api/TodoItems/{識別碼} | 依識別碼取得項目 | 無 | 待辦事項|
|POST /api/TodoItems | 新增記錄 | 待辦事項 | 待辦事項 |
|PUT /api/TodoItems/{識別碼} | 更新現有的項目 &nbsp; | 待辦事項 | 無 |
|刪除/Api/todoitems/{識別碼} &nbsp;&nbsp; | 刪除專案 &nbsp;&nbsp; | 無 | 無|

下圖顯示應用程式的設計。

![左側方塊代表用戶端。 它會送出要求並接收來自應用程式 (右側繪製的方塊) 的回應。 在應用程式方塊中，三個方塊代表控制器、模型以及資料存取層。 要求進入應用程式的控制器，而在控制器與資料存取層之間進行讀取/寫入作業。 模型會序列化並在回應中傳回至用戶端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a>必要條件2。1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a>建立 Web 專案2。1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 [ **檔案** ] 功能表選取 [ **新增** > **專案**]。
* 選取 **ASP.NET Core Web 應用程式** 範本，然後按一下 [下一步]。
* 將專案命名為 *TodoApi*，然後按一下 [建立]。
* 在 [建立新的 ASP.NET Core Web 應用程式] 對話方塊中，確認選取 [.NET Core] 和 [ASP.NET Core 2.2]。 選取 **API** 範本，然後按一下 [建立]。 請 **勿** 選取 [Enable Docker Support] \(啟用 Docker 支援\)。

![VS 新增專案對話方塊](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 將目錄 (`cd`) 變更為包含專案資料夾的資料夾。
* 執行下列命令：

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  這些命令會建立新的 Web API 專案，並開啟新專案資料夾中的新 Visual Studio Code 執行個體。

* 當出現對話方塊詢問您是否要將所需的資產新增至專案時，選取 [是]。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 選取 [檔案]**[新增解決方案]** > 。

  ![macOS 新增方案](first-web-api-mac/_static/sln.png)

* 在8.6 版之前的 Visual Studio for Mac 中，選取 [ **.net Core**  >  **應用程式**  >  **API**  >  **]**。 在8.6 版或更新版本中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **API**  >  **]**。
  
* 在 [ **設定新的 ASP.NET Core WEB API** ] 對話方塊中，選取最新的 .net Core 2.X **目標 Framework**。 選取 [下一步] 。

* 針對 [專案名稱] 輸入 *TodoApi*，然後選取 [建立]。

  ![設定對話方塊](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a>測試 API 2。1

專案範本會建立 `values` API。 從瀏覽器呼叫 `Get` 方法來測試應用程式。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

按 Ctrl+F5 執行應用程式。 Visual Studio 會啟動瀏覽器並巡覽至 `https://localhost:<port>/api/values`，其中 `<port>` 是隨機選擇的通訊埠編號。

如果出現對話方塊詢問您是否應該信任 IIS Express 憑證，請選取 [是]。 在接著出現的 [安全性警告] 對話方塊中，選取 [是]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按 Ctrl+F5 執行應用程式。 在瀏覽器中，前往下列 URL：`https://localhost:5001/api/values`。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

選取 [**執行**  >  **開始調試** 程式] 以啟動應用程式。 Visual Studio for Mac 會啟動瀏覽器並巡覽至 `https://localhost:<port>`，其中 `<port>` 是隨機選擇的連接埠號碼。 傳回 HTTP 404 (找不到) 錯誤。 將 `/api/values` 附加至 URL (將 URL 變更為 `https://localhost:<port>/api/values`)。

---

隨即傳回下列 JSON：

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a>新增模型類別2。1

「模型」是代表應用程式所管理資料的一組類別。 此應用程式的模型是單一 `TodoItem` 類別。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **方案總管** 中，以滑鼠右鍵按一下專案。 選取 **[**  >  **新增資料夾**]。 為資料夾命名 *Models* 。

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。 將類別命名為 *TodoItem*，然後選取 [新增]。

* 使用下列程式碼取代範本程式碼：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為的資料夾 *Models* 。

* `TodoItem`使用下列程式碼，將類別新增至 *Models* 資料夾：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 以滑鼠右鍵按一下專案。 選取 **[**  >  **新增資料夾**]。 為資料夾命名 *Models* 。

  ![新增資料夾](first-web-api-mac/_static/folder.png)

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 > [**新增** 檔案 > **一般**] > **空白類別**。

* 將類別命名為 *TodoItem*，然後按一下 [新增]。

* 使用下列程式碼取代範本程式碼：

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` 屬性的功能相當於關聯式資料庫中的唯一索引鍵。

模型類別可移至專案中的任何位置，但依照慣例，會 *Models* 使用資料夾。

## <a name="add-a-database-context-21"></a>新增資料庫內容2。1

「資料庫內容」是為資料模型協調 Entity Framework 功能的主要類別。 此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以滑鼠右鍵按一下 *Models* 資料夾，然後選取 [**加入**  >  **類別**]。 將類別命名為 *TodoContext*，然後按一下 [新增]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 將 `TodoContext` 類別新增至 *Models* 資料夾。

---

* 使用下列程式碼取代範本程式碼：

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a>註冊資料庫內容2。1

在 ASP.NET Core 中，資料庫內容等服務必須向[相依性插入 (DI)](xref:fundamentals/dependency-injection) 容器註冊。 此容器會將服務提供給控制器。

使用下列醒目提示的程式碼更新 *Startup.cs*：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

上述程式碼：

* 移除未使用的 `using` 宣告。
* 將資料庫內容新增至 DI 容器。
* 指定資料庫內容將會使用記憶體內部資料庫。

## <a name="add-a-controller-21"></a>新增控制器2。1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 以滑鼠右鍵按一下 *Controllers* 資料夾。
* 選取 [ **加入** > **新專案**]。
* 在 [新增項目] 對話方塊中，選取 [API 控制器類別] 範本。
* 將類別命名為 *TodoController*，然後選取 [新增]。

  ![在搜尋方塊中輸入 controller 且已選取 Web API 控制器的 [新增項目] 對話方塊](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 在 *Controllers* 資料夾中，建立名為 `TodoController` 的類別。

---

* 使用下列程式碼取代範本程式碼：

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

上述程式碼：

* 定義不含方法的 API 控制器類別。
* 標記具有屬性的類別 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 。 這個屬性表示控制器會回應 Web API 要求。 如需屬性所啟用之特定行為的相關資訊，請參閱 <xref:web-api/index>。
* 使用 DI 將資料庫內容 (`TodoContext`) 插入到控制器中。 控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。
* 如果資料庫是空的，請將名為 `Item1` 的項目新增至資料庫。 此程式碼是在建構函式中，因此每次執行都會有新的 HTTP 要求。 如果您刪除所有項目，則建構函式會在下次呼叫 API 方法時重新建立 `Item1`。 因此看起來雖然像是刪除失敗，但實際為成功。

## <a name="add-get-methods-21"></a>新增 Get 方法2。1

若要提供擷取待辦事項的 API，請在 `TodoController` 類別中新增下列方法：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

這些方法會實作兩個 GET 端點：

* `GET /api/todo`
* `GET /api/todo/{id}`

如果應用程式仍在執行，請將其停止。 然後重新予以執行以包含最新的變更。

從瀏覽器呼叫這兩個端點來測試應用程式。 例如：

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

以下是呼叫 `GetTodoItems` 所產生的 HTTP 回應：

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a>路由和 URL 路徑2。1

[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)屬性代表回應 HTTP GET 要求的方法。 每個方法的 URL 路徑的建構方式如下：

* 一開始在控制器的 `Route` 屬性中使用範本字串：

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* 以控制器的名稱取代 `[controller]`，也就是將控制器類別名稱減去 "Controller" 字尾。 在此範例中，控制器類別名稱是 **Todo** Controller，因此容器名稱是 "todo"。 ASP.NET Core [路由](xref:mvc/controllers/routing)不區分大小寫。
* 如果 `[HttpGet]` 屬性具有路由範本 (例如 `[HttpGet("products")]`)，請將其附加到路徑。 此範例不使用範本。 如需詳細資訊，請參閱[使用 Http[Verb] 屬性的屬性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

在下列 `GetTodoItem` 方法中，`"{id}"` 是待辦事項唯一識別碼的預留位置變數。 在叫用 `GetTodoItem` 時，會將 URL 中的 `"{id}"` 值提供給方法的 `id` 參數。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a>傳回值2。1

和方法的傳回型 `GetTodoItems` 別 `GetTodoItem` 為 [ActionResult \<T> 類型](xref:web-api/action-return-types#actionresultt-type)。 ASP.NET Core 會自動將物件序列化為 [JSON](https://www.json.org/)，並將 JSON 寫入至回應訊息的本文。 此傳回型別的回應碼為 200，假設沒有任何未處理的例外狀況。 未處理的例外狀況會轉譯成 5xx 錯誤。

`ActionResult` 傳回型別可代表各種 HTTP 狀態碼。 例如，`GetTodoItem` 可傳回兩個不同的狀態值：

* 如果沒有任何專案符合要求的識別碼，方法會傳回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 錯誤碼。
* 否則，方法會傳回 200 與 JSON 回應本文。 傳回 `item` 會導致 HTTP 200 回應。

## <a name="test-the-gettodoitems-method-21"></a>測試 GetTodoItems 方法2。1

本教學課程使用 Postman 來測試 Web API。

* 安裝 [Postman](https://www.getpostman.com/downloads/)。
* 啟動 Web 應用程式。
* 啟動 Postman。
* 停用 **SSL 憑證驗證**。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 [檔案]**[設定]** >  ([一般] 索引標籤)，停用 [SSL 憑證驗證]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 從 **Postman**  >  **喜好** 設定 (**一般**] 索引標籤) ，停用 **SSL 憑證驗證**。 或者，選取扳手並選取 [設定]，然後停用 [SSL 憑證驗證]。

---
  
> [!WARNING]
> 在測試控制器之後，請重新啟用 [SSL certificate verification] \(SSL 憑證驗證\)。

* 建立新的要求。
  * 將 HTTP 方法設定為 **GET**。
  * 將要求 URI 設定為 `https://localhost:<port>/api/todo` 。 例如： `https://localhost:5001/api/todo` 。
* 在 Postman 中，設定 [Two pane view] \(雙窗格檢視\)。
* 選取 [傳送]。

![Postman 與 GET 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a>新增 Create 方法2。1

在 *Controllers/TodoController.cs* 內部新增下列 `PostTodoItem` 方法： 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

上述程式碼是 HTTP POST 方法，如屬性所指示 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 。 該方法會從 HTTP 要求本文取得待辦事項的值。

`CreatedAtAction` 方法：

* 成功時會傳回 HTTP 201 狀態碼。 對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是 HTTP 201。
* 將 `Location` 標頭加到回應中。 `Location` 標頭指定新建立之待辦事項的 URI。 如需詳細資訊，請參閱 [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (已建立 10.2.2 201)。
* 參考 `GetTodoItem` 動作以建立 `Location` 標頭的 URI。 C# `nameof` 關鍵字是用來避免在 `CreatedAtAction` 呼叫中以硬式編碼方式寫入動作名稱。

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a>測試 PostTodoItem 方法2。1

* 建置專案。
* 在 Postman 中，將 HTTP 方法設定為 `POST`。
* 將 URI 設定為 `https://localhost:<port>/api/Todo` 。 例如： `https://localhost:5001/api/Todo` 。
* 選取 [Body] \(本文\) 索引標籤。
* 選取 [原始] 選項按鈕。
* 將類型設定為 **JSON (application/json)**。
* 在要求本文中，針對待辦項目輸入 JSON：

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* 選取 [傳送]。

  ![Postman 與建立要求](first-web-api/_static/create.png)

  如果您收到 405「不允許的方法」錯誤，可能是由於新增 `PostTodoItem` 方法之後未編譯專案所導致。

### <a name="test-the-location-header-uri-21"></a>測試位置標頭 URI 2。1

* 在 [回應] 窗格中選取 [標頭] 索引標籤。
* 複製 [位置] 標頭值：

  ![Postman 主控台的 [標頭] 索引標籤](first-web-api/_static/pmc2.png)

* 將方法設定為 GET。
* 將 URI 設定為 `https://localhost:<port>/api/TodoItems/2` 。 例如： `https://localhost:5001/api/TodoItems/2` 。
* 選取 [傳送]。

## <a name="add-a-puttodoitem-method-21"></a>新增 PutTodoItem 方法2。1

請新增下列 `PutTodoItem` 方法：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` 類似於 `PostTodoItem`，但是會使用 HTTP PUT。 回應為 [204 (沒有內容) ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 根據 HTTP 規格，PUT 要求需要用戶端傳送整個更新的實體，而不只是變更。 若要支援部分更新，請使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。

如果在呼叫 `PutTodoItem` 時發生錯誤，請呼叫 `GET` 以確保資料庫中有項目。

### <a name="test-the-puttodoitem-method-21"></a>測試 PutTodoItem 方法2。1

這個範例會使用必須在每次啟動應用程式時初始化的記憶體內部資料庫。 資料庫中必須有項目，您才能進行 PUT 呼叫。 在進行 PUT 呼叫之前，請先呼叫 GET 以確保資料庫中有專案。

更新識別碼為1的待辦事項，並將其名稱設定為「摘要魚」：

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

下圖顯示 Postman 更新：

![顯示「204 (沒有內容) 回應」的 Postman 主控台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a>新增 DeleteTodoItem 方法2。1

請新增下列 `DeleteTodoItem` 方法：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` 回應是 [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) \(204 (沒有內容)\)。

### <a name="test-the-deletetodoitem-method-21"></a>測試 DeleteTodoItem 方法2。1

使用 Postman 刪除待辦事項：

* 將方法設定為 `DELETE`。
* 設定要刪除之物件的 URI (例如 `https://localhost:5001/api/todo/1`) 。
* 選取 [傳送]。

範例應用程式可讓您刪除所有項目。 但刪除最後一個項目之後，模型類別建構函式會在下次呼叫 API 時建立新的項目。

## <a name="call-the-web-api-with-javascript-21"></a>使用 JavaScript 2.1 呼叫 web API

在此節中，將會新增 HTML 網頁，以使用 JavaScript 來呼叫 Web API。 jQuery 會起始要求。 JavaScript 會使用來自 Web API 回應的詳細資料來更新頁面。

藉由使用下列反白顯示的程式碼更新 *Startup.cs*，來設定應用程式 [提供靜態檔案](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)並 [啟用預設檔案對應](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

在專案目錄中建立 *wwwroot* 資料夾。

將名為 *index.html* 的 HTML 檔案新增至 *wwwroot* 目錄。 將其內容取代為下列標記：

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

將名為 *site.js* 的 JavaScript 檔案新增至 *wwwroot* 目錄。 以下列程式碼取代其內容：

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

若要在本機測試 HTML 網頁，可能需要變更 ASP.NET Core 專案的啟動設定：

* 開啟 *Properties\launchSettings.json*。
* 移除 `launchUrl` 屬性，以強制在專案的預設檔案 *index.html* 開啟應用程式 &mdash; 。

此範例會呼叫 Web API 的所有 CRUD 方法。 以下是關於呼叫 API 的說明。

### <a name="get-a-list-of-to-do-items-21"></a>取得待辦事項的清單2。1

jQuery 會將 HTTP GET 要求傳送至 web API，該 API 會傳回代表待辦事項陣列的 JSON。 如果要求成功，則會叫用 `success` 回呼函式。 在回呼中，DOM 已使用待辦事項資訊進行更新。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a>新增待辦事項2。1

jQuery 會使用要求主體中的待辦事項來傳送 HTTP POST 要求。 `accepts` 和 `contentType` 選項都設定為 `application/json`，以指定接收和傳送的媒體類型。 待辦事項會使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 轉換成 JSON。 當 API 傳回成功狀態碼時，會叫用 `getData` 函式來更新 HTML 資料表。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a>更新待辦事項2。1

更新待辦事項類似於新增待辦事項。 `url` 會變更為新增項目的唯一識別碼，而 `type` 是 `PUT`。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a>刪除待辦事項2。1

刪除待辦事項的達成方法是將 AJAX 呼叫的 `type` 設定為 `DELETE`，並在 URL 中指定項目的唯一識別碼。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a>將驗證支援新增至 web API 2。1

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a>其他資源2。1

[檢視或下載本教學課程的範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。 請參閱[如何下載](xref:index#how-to-download-a-sample)。

如需詳細資訊，請參閱下列資源：

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [本教學課程的 YouTube 版本](https://www.youtube.com/watch?v=TTkhEyGBfAk)
