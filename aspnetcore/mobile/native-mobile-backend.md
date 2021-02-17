---
title: 使用 ASP.NET Core 建立原生行動應用程式的後端服務
author: rick-anderson
description: 了解如何使用 ASP.NET Core MVC 建立後端服務，以支援原生行動應用程式。
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564017"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>使用 ASP.NET Core 建立原生行動應用程式的後端服務

由 [James Montemagno](https://twitter.com/JamesMontemagno)

行動裝置應用程式可以與 ASP.NET Core 後端服務通訊。 如需如何從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務的指示，請參閱[從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)。

[檢視或下載簡易後端服務程式碼範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>原生行動應用程式範例

本教學課程示範如何使用 ASP.NET Core 來建立後端服務，以支援原生行動應用程式。 它使用 [TodoRest 應用程式](/xamarin/xamarin-forms/data-cloud/consuming/rest) 做為其原生用戶端，其中包含適用于 Android、IOS 和 Windows 的個別原生用戶端。 您可以遵循連結的教學課程來建立原生應用程式 (並安裝必要的免費 Xamarin 工具)，也可以下載 Xamarin 範例解決方案。 Xamarin 範例包含 ASP.NET Core 的 Web API 服務專案，這篇文章的 ASP.NET Core 應用程式會取代 (，而不需要用戶端) 所需的變更。

![To Do Rest 應用程式在 Android 智慧型手機上執行](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>功能

[TodoREST 應用程式](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)支援列出、新增、刪除和更新 To-Do 專案。 每個項目都有識別碼、名稱、記事和標示其是否已完成的屬性。

項目的主要檢視，如上所示，會列出每個項目的名稱，並使用勾選記號表示其是否已完成。

點選 `+` 圖示便會開啟 [新增項目] 對話方塊：

![[新增項目] 對話方塊](native-mobile-backend/_static/todo-android-new-item.png)

在主要清單畫面中點選項目，便會開啟 [編輯] 對話方塊，讓使用者修改項目的名稱、記事及完成狀態，或是刪除該項目：

![[編輯項目] 對話方塊](native-mobile-backend/_static/todo-android-edit-item.png)

若要針對在電腦上執行的下一節所建立的 ASP.NET Core 應用程式自行測試，請更新應用程式的 [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) 常數。

Android 模擬器不會在本機電腦上執行，並使用回送 IP (10.0.2.2) 來與本機電腦通訊。 利用 [Xamarin DeviceInfo](/xamarin/essentials/device-information/) 來偵測系統正在執行的作業系統，以使用正確的 URL。

流覽至 [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) 專案並開啟檔案 [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) 。 *Constants.cs* 檔案包含下列設定。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

您可以選擇性地將 web 服務部署至雲端服務（例如 Azure），並更新 `RestUrl` 。

## <a name="creating-the-aspnet-core-project"></a>建立 ASP.NET Core 專案

在 Visual Studio 中建立新的 ASP.NET Core Web 應用程式。 選擇 [Web API] 範本。 將專案命名為 *TodoAPI*。

![新的 ASP.NET Web 應用程式對話方塊，當中已選取了 Web API 專案範本](native-mobile-backend/_static/web-api-template.png)

應用程式應該回應對埠5000提出的所有要求，包括行動用戶端的純文字 HTTP 流量。 更新 *Startup.cs* <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 不會在開發期間執行：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> 直接執行應用程式，而不是在 IIS Express 後方執行。 IIS Express 預設會忽略非本機要求。 從命令提示字元執行 [dotnet 執行](/dotnet/core/tools/dotnet-run) ，或從 [Visual Studio] 工具列的 [Debug 目標] 下拉式清單中選擇應用程式名稱設定檔。

新增一個模型類別來代表待辦項目。 使用屬性標記必要欄位 `[Required]` ：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

API 方法需要一些方式才能操作資料。 使用原始 Xamarin 範本使用的相同 `ITodoRepository` 介面：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

此範例中，實作只會使用私用項目集合：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

設定 *Startup.cs* 中的實作：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>建立控制器

將新的控制器新增至專案 [>todoitemscontroller](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)。 它應該繼承自 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。 新增一個 `Route` 屬性來表示控制器將會處理所有傳送到以 `api/todoitems` 開頭之路徑的要求。 路由中的 `[controller]` 權杖會由控制器的名稱取代 (省略 `Controller` 尾碼)，特別在全域路由時將會非常有幫助。 深入了解[路由](../fundamentals/routing.md)。

控制器需要一個 `ITodoRepository` 才能發揮功能，請在控制器的建構函式中要求此類型的執行個體。 在執行階段，這個執行個體會使用架構的[相依性插入](../fundamentals/dependency-injection.md)支援提供。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

這個 API 支援四種不同的 HTTP 動詞命令來在資料來源上執行 CRUD (建立、讀取、更新、刪除) 作業。 其中最簡單的便是「讀取」作業，其對應到 HTTP GET 要求。

### <a name="reading-items"></a>讀取項目

您可以藉由對 `List` 方法傳送 GET 要求來要求項目清單。 `List` 方法上的 `[HttpGet]` 屬性表示這項動作應該僅用於處理 GET 要求。 此動作的路由為在控制器上指定的路由。 您不見得需要使用動作名稱作為路由的一部分。 您只需要確認每個動作都有一個唯一且明確的路由。 路由屬性可套用在控制器和方法層級上，以建置特定的路由。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

方法會傳回 `List` 200 OK 回應碼以及所有待辦事項（序列化為 JSON）。

您可以使用各種不同的工具測試您的新 API 方法，例如 [Postman](https://www.getpostman.com/docs/)，如這裡所示：

![Postman 主控台會顯示針對待辦項目的 GET 要求，以及顯示傳回了三個項目之 JSON 的回應主體](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>建立項目

根據慣例，建立新的資料項目會對應到 HTTP POST 動詞命令。 `Create`方法已套用 `[HttpPost]` 屬性並接受 `TodoItem` 實例。 由於自 `item` 變數是在 POST 的主體中傳遞，因此這個參數會指定 `[FromBody]` 屬性。

在方法中，項目會經過有效性的檢查，以及是否先前存在過資料存放區中。若沒有發現任何問題，則該項目便會新增至存放庫中。 檢查 `ModelState.IsValid` 會執行 [模型驗證](../mvc/models/validation.md)，並且應該要在每個接受使用者輸入的 API 方法中執行。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

此範例會使用 `enum` 包含傳遞給行動用戶端的錯誤碼：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

藉由選擇 POST 動詞並在要求主體中使用 JSON 格式提供新物件，來使用 Postman 測試新增新項目。 您也應該新增一個要求標頭，將 `Content-Type` 指定為 `application/json`。

![Postman 主控台顯示 POST 及回應](native-mobile-backend/_static/postman-post.png)

方法會在回應中傳回新建立的項目。

### <a name="updating-items"></a>更新項目

修改記錄會使用到 HTTP PUT 要求。 除了這項變更之外，`Edit` 方法與 `Create` 方法基本上都完全相同。 請注意，若找不到記錄，`Edit` 動作會傳回一個 `NotFound` (404) 回應。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

若要使用 Postman 進行測試，請將動詞變更為 PUT。 在要求主體中指定更新物件資料。

![Postman 主控台顯示 PUT 及回應](native-mobile-backend/_static/postman-put.png)

這個方法會在成功時傳回一個 `NoContent` (204) 回應 (為了與先前存在的 API 保持一致)。

### <a name="deleting-items"></a>刪除項目

刪除記錄可透過傳送 DELETE 要求到服務，並傳遞要刪除之項目的識別碼來完成。 與更新時一樣，若要求項目不存在，使用者便會接收到 `NotFound` 回應。 否則，成功的要求會取得一個 `NoContent` (204) 回應。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

請注意，當測試刪除功能時，要求主體中不需要任何內容。

![Postman 主控台顯示 DELETE 及回應](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>防止過度張貼

範例應用程式目前會公開整個 `TodoItem` 物件。 生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。 這項功能有許多原因，而且安全性是主要的原因。 模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。 此文章中使用 **DTO** 。

DTO 可以用來：

* 防止過度張貼。
* 隱藏用戶端不應該看到的屬性。
* 略過某些屬性，以減少承載大小。
* 壓平合併包含嵌套物件的物件圖形。 簡維物件圖形對於用戶端來說可能更方便。

若要示範 DTO 方法，請參閱 [防止過度張貼](xref:tutorials/first-web-api#prevent-over-posting)

## <a name="common-web-api-conventions"></a>常見 Web API 慣例

當您為您的應用程式開發後端服務時，您可能會想要想出一個一致的慣例組或原則來處理跨領域關注。 例如，在上述的服務中，要求找不到的特定記錄會讓使用者接收到 `NotFound` 回應，而非 `BadRequest` 回應。 同樣地，傳送到此服務的命令在傳遞到模型繫結類型時，也會檢查 `ModelState.IsValid`並針對無效的模型類型傳回 `BadRequest`。

當您找到了適用於您 API 的常見原則時，您通常可以在[篩選條件](../mvc/controllers/filters.md)中封裝它。 深入了解[如何在 ASP.NET Core MVC 應用程式中封裝常見的 API 原則](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)。

## <a name="additional-resources"></a>其他資源

- [Xamarin： Web 服務驗證](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin：使用 RESTful Web 服務](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn：在 Xamarin 應用程式中取用 REST web 服務](/learn/modules/consume-rest-services/)
- [Microsoft Learn：使用 ASP.NET Core 建立 web API](/learn/modules/build-web-api-aspnet-core/)
