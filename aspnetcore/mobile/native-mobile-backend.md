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
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="1e44a-103">使用 ASP.NET Core 建立原生行動應用程式的後端服務</span><span class="sxs-lookup"><span data-stu-id="1e44a-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="1e44a-104">由 [James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="1e44a-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="1e44a-105">行動裝置應用程式可以與 ASP.NET Core 後端服務通訊。</span><span class="sxs-lookup"><span data-stu-id="1e44a-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="1e44a-106">如需如何從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務的指示，請參閱[從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)。</span><span class="sxs-lookup"><span data-stu-id="1e44a-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="1e44a-107">檢視或下載簡易後端服務程式碼範例</span><span class="sxs-lookup"><span data-stu-id="1e44a-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="1e44a-108">原生行動應用程式範例</span><span class="sxs-lookup"><span data-stu-id="1e44a-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="1e44a-109">本教學課程示範如何使用 ASP.NET Core 來建立後端服務，以支援原生行動應用程式。</span><span class="sxs-lookup"><span data-stu-id="1e44a-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="1e44a-110">它使用 [TodoRest 應用程式](/xamarin/xamarin-forms/data-cloud/consuming/rest) 做為其原生用戶端，其中包含適用于 Android、IOS 和 Windows 的個別原生用戶端。</span><span class="sxs-lookup"><span data-stu-id="1e44a-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="1e44a-111">您可以遵循連結的教學課程來建立原生應用程式 (並安裝必要的免費 Xamarin 工具)，也可以下載 Xamarin 範例解決方案。</span><span class="sxs-lookup"><span data-stu-id="1e44a-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="1e44a-112">Xamarin 範例包含 ASP.NET Core 的 Web API 服務專案，這篇文章的 ASP.NET Core 應用程式會取代 (，而不需要用戶端) 所需的變更。</span><span class="sxs-lookup"><span data-stu-id="1e44a-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![To Do Rest 應用程式在 Android 智慧型手機上執行](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="1e44a-114">功能</span><span class="sxs-lookup"><span data-stu-id="1e44a-114">Features</span></span>

<span data-ttu-id="1e44a-115">[TodoREST 應用程式](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)支援列出、新增、刪除和更新 To-Do 專案。</span><span class="sxs-lookup"><span data-stu-id="1e44a-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="1e44a-116">每個項目都有識別碼、名稱、記事和標示其是否已完成的屬性。</span><span class="sxs-lookup"><span data-stu-id="1e44a-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="1e44a-117">項目的主要檢視，如上所示，會列出每個項目的名稱，並使用勾選記號表示其是否已完成。</span><span class="sxs-lookup"><span data-stu-id="1e44a-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="1e44a-118">點選 `+` 圖示便會開啟 [新增項目] 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="1e44a-118">Tapping the `+` icon opens an add item dialog:</span></span>

![[新增項目] 對話方塊](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="1e44a-120">在主要清單畫面中點選項目，便會開啟 [編輯] 對話方塊，讓使用者修改項目的名稱、記事及完成狀態，或是刪除該項目：</span><span class="sxs-lookup"><span data-stu-id="1e44a-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![[編輯項目] 對話方塊](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="1e44a-122">若要針對在電腦上執行的下一節所建立的 ASP.NET Core 應用程式自行測試，請更新應用程式的 [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) 常數。</span><span class="sxs-lookup"><span data-stu-id="1e44a-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="1e44a-123">Android 模擬器不會在本機電腦上執行，並使用回送 IP (10.0.2.2) 來與本機電腦通訊。</span><span class="sxs-lookup"><span data-stu-id="1e44a-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="1e44a-124">利用 [Xamarin DeviceInfo](/xamarin/essentials/device-information/) 來偵測系統正在執行的作業系統，以使用正確的 URL。</span><span class="sxs-lookup"><span data-stu-id="1e44a-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="1e44a-125">流覽至 [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) 專案並開啟檔案 [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) 。</span><span class="sxs-lookup"><span data-stu-id="1e44a-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="1e44a-126">*Constants.cs* 檔案包含下列設定。</span><span class="sxs-lookup"><span data-stu-id="1e44a-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="1e44a-127">您可以選擇性地將 web 服務部署至雲端服務（例如 Azure），並更新 `RestUrl` 。</span><span class="sxs-lookup"><span data-stu-id="1e44a-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="1e44a-128">建立 ASP.NET Core 專案</span><span class="sxs-lookup"><span data-stu-id="1e44a-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="1e44a-129">在 Visual Studio 中建立新的 ASP.NET Core Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="1e44a-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="1e44a-130">選擇 [Web API] 範本。</span><span class="sxs-lookup"><span data-stu-id="1e44a-130">Choose the Web API template.</span></span> <span data-ttu-id="1e44a-131">將專案命名為 *TodoAPI*。</span><span class="sxs-lookup"><span data-stu-id="1e44a-131">Name the project *TodoAPI*.</span></span>

![新的 ASP.NET Web 應用程式對話方塊，當中已選取了 Web API 專案範本](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="1e44a-133">應用程式應該回應對埠5000提出的所有要求，包括行動用戶端的純文字 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="1e44a-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="1e44a-134">更新 *Startup.cs* <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 不會在開發期間執行：</span><span class="sxs-lookup"><span data-stu-id="1e44a-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="1e44a-135">直接執行應用程式，而不是在 IIS Express 後方執行。</span><span class="sxs-lookup"><span data-stu-id="1e44a-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="1e44a-136">IIS Express 預設會忽略非本機要求。</span><span class="sxs-lookup"><span data-stu-id="1e44a-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="1e44a-137">從命令提示字元執行 [dotnet 執行](/dotnet/core/tools/dotnet-run) ，或從 [Visual Studio] 工具列的 [Debug 目標] 下拉式清單中選擇應用程式名稱設定檔。</span><span class="sxs-lookup"><span data-stu-id="1e44a-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="1e44a-138">新增一個模型類別來代表待辦項目。</span><span class="sxs-lookup"><span data-stu-id="1e44a-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="1e44a-139">使用屬性標記必要欄位 `[Required]` ：</span><span class="sxs-lookup"><span data-stu-id="1e44a-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="1e44a-140">API 方法需要一些方式才能操作資料。</span><span class="sxs-lookup"><span data-stu-id="1e44a-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="1e44a-141">使用原始 Xamarin 範本使用的相同 `ITodoRepository` 介面：</span><span class="sxs-lookup"><span data-stu-id="1e44a-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="1e44a-142">此範例中，實作只會使用私用項目集合：</span><span class="sxs-lookup"><span data-stu-id="1e44a-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="1e44a-143">設定 *Startup.cs* 中的實作：</span><span class="sxs-lookup"><span data-stu-id="1e44a-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="1e44a-144">建立控制器</span><span class="sxs-lookup"><span data-stu-id="1e44a-144">Creating the Controller</span></span>

<span data-ttu-id="1e44a-145">將新的控制器新增至專案 [>todoitemscontroller](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)。</span><span class="sxs-lookup"><span data-stu-id="1e44a-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="1e44a-146">它應該繼承自 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。</span><span class="sxs-lookup"><span data-stu-id="1e44a-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1e44a-147">新增一個 `Route` 屬性來表示控制器將會處理所有傳送到以 `api/todoitems` 開頭之路徑的要求。</span><span class="sxs-lookup"><span data-stu-id="1e44a-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="1e44a-148">路由中的 `[controller]` 權杖會由控制器的名稱取代 (省略 `Controller` 尾碼)，特別在全域路由時將會非常有幫助。</span><span class="sxs-lookup"><span data-stu-id="1e44a-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="1e44a-149">深入了解[路由](../fundamentals/routing.md)。</span><span class="sxs-lookup"><span data-stu-id="1e44a-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="1e44a-150">控制器需要一個 `ITodoRepository` 才能發揮功能，請在控制器的建構函式中要求此類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="1e44a-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="1e44a-151">在執行階段，這個執行個體會使用架構的[相依性插入](../fundamentals/dependency-injection.md)支援提供。</span><span class="sxs-lookup"><span data-stu-id="1e44a-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="1e44a-152">這個 API 支援四種不同的 HTTP 動詞命令來在資料來源上執行 CRUD (建立、讀取、更新、刪除) 作業。</span><span class="sxs-lookup"><span data-stu-id="1e44a-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="1e44a-153">其中最簡單的便是「讀取」作業，其對應到 HTTP GET 要求。</span><span class="sxs-lookup"><span data-stu-id="1e44a-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="1e44a-154">讀取項目</span><span class="sxs-lookup"><span data-stu-id="1e44a-154">Reading Items</span></span>

<span data-ttu-id="1e44a-155">您可以藉由對 `List` 方法傳送 GET 要求來要求項目清單。</span><span class="sxs-lookup"><span data-stu-id="1e44a-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="1e44a-156">`List` 方法上的 `[HttpGet]` 屬性表示這項動作應該僅用於處理 GET 要求。</span><span class="sxs-lookup"><span data-stu-id="1e44a-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="1e44a-157">此動作的路由為在控制器上指定的路由。</span><span class="sxs-lookup"><span data-stu-id="1e44a-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="1e44a-158">您不見得需要使用動作名稱作為路由的一部分。</span><span class="sxs-lookup"><span data-stu-id="1e44a-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="1e44a-159">您只需要確認每個動作都有一個唯一且明確的路由。</span><span class="sxs-lookup"><span data-stu-id="1e44a-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="1e44a-160">路由屬性可套用在控制器和方法層級上，以建置特定的路由。</span><span class="sxs-lookup"><span data-stu-id="1e44a-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="1e44a-161">方法會傳回 `List` 200 OK 回應碼以及所有待辦事項（序列化為 JSON）。</span><span class="sxs-lookup"><span data-stu-id="1e44a-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="1e44a-162">您可以使用各種不同的工具測試您的新 API 方法，例如 [Postman](https://www.getpostman.com/docs/)，如這裡所示：</span><span class="sxs-lookup"><span data-stu-id="1e44a-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Postman 主控台會顯示針對待辦項目的 GET 要求，以及顯示傳回了三個項目之 JSON 的回應主體](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="1e44a-164">建立項目</span><span class="sxs-lookup"><span data-stu-id="1e44a-164">Creating Items</span></span>

<span data-ttu-id="1e44a-165">根據慣例，建立新的資料項目會對應到 HTTP POST 動詞命令。</span><span class="sxs-lookup"><span data-stu-id="1e44a-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="1e44a-166">`Create`方法已套用 `[HttpPost]` 屬性並接受 `TodoItem` 實例。</span><span class="sxs-lookup"><span data-stu-id="1e44a-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="1e44a-167">由於自 `item` 變數是在 POST 的主體中傳遞，因此這個參數會指定 `[FromBody]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="1e44a-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="1e44a-168">在方法中，項目會經過有效性的檢查，以及是否先前存在過資料存放區中。若沒有發現任何問題，則該項目便會新增至存放庫中。</span><span class="sxs-lookup"><span data-stu-id="1e44a-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="1e44a-169">檢查 `ModelState.IsValid` 會執行 [模型驗證](../mvc/models/validation.md)，並且應該要在每個接受使用者輸入的 API 方法中執行。</span><span class="sxs-lookup"><span data-stu-id="1e44a-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="1e44a-170">此範例會使用 `enum` 包含傳遞給行動用戶端的錯誤碼：</span><span class="sxs-lookup"><span data-stu-id="1e44a-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="1e44a-171">藉由選擇 POST 動詞並在要求主體中使用 JSON 格式提供新物件，來使用 Postman 測試新增新項目。</span><span class="sxs-lookup"><span data-stu-id="1e44a-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="1e44a-172">您也應該新增一個要求標頭，將 `Content-Type` 指定為 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="1e44a-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Postman 主控台顯示 POST 及回應](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="1e44a-174">方法會在回應中傳回新建立的項目。</span><span class="sxs-lookup"><span data-stu-id="1e44a-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="1e44a-175">更新項目</span><span class="sxs-lookup"><span data-stu-id="1e44a-175">Updating Items</span></span>

<span data-ttu-id="1e44a-176">修改記錄會使用到 HTTP PUT 要求。</span><span class="sxs-lookup"><span data-stu-id="1e44a-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="1e44a-177">除了這項變更之外，`Edit` 方法與 `Create` 方法基本上都完全相同。</span><span class="sxs-lookup"><span data-stu-id="1e44a-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="1e44a-178">請注意，若找不到記錄，`Edit` 動作會傳回一個 `NotFound` (404) 回應。</span><span class="sxs-lookup"><span data-stu-id="1e44a-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="1e44a-179">若要使用 Postman 進行測試，請將動詞變更為 PUT。</span><span class="sxs-lookup"><span data-stu-id="1e44a-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="1e44a-180">在要求主體中指定更新物件資料。</span><span class="sxs-lookup"><span data-stu-id="1e44a-180">Specify the updated object data in the Body of the request.</span></span>

![Postman 主控台顯示 PUT 及回應](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="1e44a-182">這個方法會在成功時傳回一個 `NoContent` (204) 回應 (為了與先前存在的 API 保持一致)。</span><span class="sxs-lookup"><span data-stu-id="1e44a-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="1e44a-183">刪除項目</span><span class="sxs-lookup"><span data-stu-id="1e44a-183">Deleting Items</span></span>

<span data-ttu-id="1e44a-184">刪除記錄可透過傳送 DELETE 要求到服務，並傳遞要刪除之項目的識別碼來完成。</span><span class="sxs-lookup"><span data-stu-id="1e44a-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="1e44a-185">與更新時一樣，若要求項目不存在，使用者便會接收到 `NotFound` 回應。</span><span class="sxs-lookup"><span data-stu-id="1e44a-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="1e44a-186">否則，成功的要求會取得一個 `NoContent` (204) 回應。</span><span class="sxs-lookup"><span data-stu-id="1e44a-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="1e44a-187">請注意，當測試刪除功能時，要求主體中不需要任何內容。</span><span class="sxs-lookup"><span data-stu-id="1e44a-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Postman 主控台顯示 DELETE 及回應](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="1e44a-189">防止過度張貼</span><span class="sxs-lookup"><span data-stu-id="1e44a-189">Prevent over-posting</span></span>

<span data-ttu-id="1e44a-190">範例應用程式目前會公開整個 `TodoItem` 物件。</span><span class="sxs-lookup"><span data-stu-id="1e44a-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="1e44a-191">生產環境應用程式通常會限制使用模型子集來輸入和傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="1e44a-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="1e44a-192">這項功能有許多原因，而且安全性是主要的原因。</span><span class="sxs-lookup"><span data-stu-id="1e44a-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="1e44a-193">模型的子集通常稱為資料傳輸物件 (DTO) 、輸入模型或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="1e44a-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="1e44a-194">此文章中使用 **DTO** 。</span><span class="sxs-lookup"><span data-stu-id="1e44a-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="1e44a-195">DTO 可以用來：</span><span class="sxs-lookup"><span data-stu-id="1e44a-195">A DTO may be used to:</span></span>

* <span data-ttu-id="1e44a-196">防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="1e44a-196">Prevent over-posting.</span></span>
* <span data-ttu-id="1e44a-197">隱藏用戶端不應該看到的屬性。</span><span class="sxs-lookup"><span data-stu-id="1e44a-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="1e44a-198">略過某些屬性，以減少承載大小。</span><span class="sxs-lookup"><span data-stu-id="1e44a-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="1e44a-199">壓平合併包含嵌套物件的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="1e44a-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="1e44a-200">簡維物件圖形對於用戶端來說可能更方便。</span><span class="sxs-lookup"><span data-stu-id="1e44a-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="1e44a-201">若要示範 DTO 方法，請參閱 [防止過度張貼](xref:tutorials/first-web-api#prevent-over-posting)</span><span class="sxs-lookup"><span data-stu-id="1e44a-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="1e44a-202">常見 Web API 慣例</span><span class="sxs-lookup"><span data-stu-id="1e44a-202">Common Web API Conventions</span></span>

<span data-ttu-id="1e44a-203">當您為您的應用程式開發後端服務時，您可能會想要想出一個一致的慣例組或原則來處理跨領域關注。</span><span class="sxs-lookup"><span data-stu-id="1e44a-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="1e44a-204">例如，在上述的服務中，要求找不到的特定記錄會讓使用者接收到 `NotFound` 回應，而非 `BadRequest` 回應。</span><span class="sxs-lookup"><span data-stu-id="1e44a-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="1e44a-205">同樣地，傳送到此服務的命令在傳遞到模型繫結類型時，也會檢查 `ModelState.IsValid`並針對無效的模型類型傳回 `BadRequest`。</span><span class="sxs-lookup"><span data-stu-id="1e44a-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="1e44a-206">當您找到了適用於您 API 的常見原則時，您通常可以在[篩選條件](../mvc/controllers/filters.md)中封裝它。</span><span class="sxs-lookup"><span data-stu-id="1e44a-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="1e44a-207">深入了解[如何在 ASP.NET Core MVC 應用程式中封裝常見的 API 原則](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)。</span><span class="sxs-lookup"><span data-stu-id="1e44a-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e44a-208">其他資源</span><span class="sxs-lookup"><span data-stu-id="1e44a-208">Additional resources</span></span>

- [<span data-ttu-id="1e44a-209">Xamarin： Web 服務驗證</span><span class="sxs-lookup"><span data-stu-id="1e44a-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="1e44a-210">Xamarin：使用 RESTful Web 服務</span><span class="sxs-lookup"><span data-stu-id="1e44a-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="1e44a-211">Microsoft Learn：在 Xamarin 應用程式中取用 REST web 服務</span><span class="sxs-lookup"><span data-stu-id="1e44a-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="1e44a-212">Microsoft Learn：使用 ASP.NET Core 建立 web API</span><span class="sxs-lookup"><span data-stu-id="1e44a-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
