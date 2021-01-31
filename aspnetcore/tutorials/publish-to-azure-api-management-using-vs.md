---
title: 使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理
author: codemillmatt
description: 瞭解如何使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理。
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217579"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="ea867-103">使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="ea867-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="ea867-104">依 [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="ea867-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="ea867-105">設定</span><span class="sxs-lookup"><span data-stu-id="ea867-105">Set up</span></span>

- <span data-ttu-id="ea867-106">如果您沒有帳戶，請開啟[免費的 Azure 帳戶](https://azure.microsoft.com/free/dotnet/)。</span><span class="sxs-lookup"><span data-stu-id="ea867-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="ea867-107">如果尚未[建立新的 AZURE API 管理實例](/azure/api-management/get-started-create-service-instance)，請加以建立。</span><span class="sxs-lookup"><span data-stu-id="ea867-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="ea867-108">[建立 WEB API 應用程式專案](xref:tutorials/first-web-api#create-a-web-project)。</span><span class="sxs-lookup"><span data-stu-id="ea867-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="ea867-109">設定應用程式</span><span class="sxs-lookup"><span data-stu-id="ea867-109">Configure the app</span></span>

<span data-ttu-id="ea867-110">將 Swagger 定義新增至 ASP.NET Core web API，可讓 Azure API 管理讀取應用程式的 API 定義。</span><span class="sxs-lookup"><span data-stu-id="ea867-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="ea867-111">完成下列步驟。</span><span class="sxs-lookup"><span data-stu-id="ea867-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="ea867-112">新增 Swagger</span><span class="sxs-lookup"><span data-stu-id="ea867-112">Add Swagger</span></span>

1. <span data-ttu-id="ea867-113">將 [Swashbuckle AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 套件新增至 ASP.NET CORE web API 專案：</span><span class="sxs-lookup"><span data-stu-id="ea867-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![設定 NuGet 對話方塊的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="ea867-115">將下行新增至 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="ea867-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="ea867-116">將下行新增至 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="ea867-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="ea867-117">變更 API 路由</span><span class="sxs-lookup"><span data-stu-id="ea867-117">Change the API routing</span></span>

<span data-ttu-id="ea867-118">接下來，您將變更存取動作所需的 URL 結構 `Get` `WeatherForecastController` 。</span><span class="sxs-lookup"><span data-stu-id="ea867-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="ea867-119">完成以下步驟：</span><span class="sxs-lookup"><span data-stu-id="ea867-119">Complete the following steps:</span></span>

1. <span data-ttu-id="ea867-120">開啟 *WeatherForecastController.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ea867-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="ea867-121">刪除 `[Route("[controller]")]` 類別層級屬性。</span><span class="sxs-lookup"><span data-stu-id="ea867-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="ea867-122">類別定義看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="ea867-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="ea867-123">將 `[Route("/")]` 屬性新增至 `Get()` 動作。</span><span class="sxs-lookup"><span data-stu-id="ea867-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="ea867-124">函式定義看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="ea867-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="ea867-125">將 web API 發佈至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea867-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="ea867-126">請完成下列步驟，將 ASP.NET Core web API 發佈至 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="ea867-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="ea867-127">將 API 應用程式發佈至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="ea867-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="ea867-128">將空白 API 新增至 Azure API 管理服務實例。</span><span class="sxs-lookup"><span data-stu-id="ea867-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="ea867-129">將 ASP.NET Core web API 應用程式發佈至 Azure API 管理服務實例。</span><span class="sxs-lookup"><span data-stu-id="ea867-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="ea867-130">將 API 應用程式發佈至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea867-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="ea867-131">請完成下列步驟，將 ASP.NET Core web API 發佈至 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="ea867-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="ea867-132">在 **方案總管** 中，以滑鼠右鍵按一下專案，然後選取 [ **發行**]：</span><span class="sxs-lookup"><span data-stu-id="ea867-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![反白顯示 [發行] 連結的已開啟操作功能表](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="ea867-134">在 [ **發行** ] 對話方塊中，選取 [ **Azure** ]，然後選取 [ **下一步]** 按鈕：</span><span class="sxs-lookup"><span data-stu-id="ea867-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![發佈對話方塊](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="ea867-136">選取 **Azure App Service (Windows)** ，然後選取 [ **下一步]** 按鈕：</span><span class="sxs-lookup"><span data-stu-id="ea867-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![發佈對話方塊：選取 App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="ea867-138">選取 [ **建立新的 Azure App Service**]。</span><span class="sxs-lookup"><span data-stu-id="ea867-138">Select **Create a new Azure App Service**.</span></span>

    ![發佈對話方塊：選取 Azure 服務實例](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="ea867-140">[建立 App Service] 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="ea867-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="ea867-141">會填入 [應用程式名稱]、[資源群組] 和 [App Service 方案] 輸入欄位。</span><span class="sxs-lookup"><span data-stu-id="ea867-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="ea867-142">您可以保留這些名稱，或變更它們。</span><span class="sxs-lookup"><span data-stu-id="ea867-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="ea867-143">選取 [建立] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ea867-143">Select the **Create** button.</span></span>

    ![建立 App Service 對話方塊](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="ea867-145">建立完成之後，對話方塊會自動關閉，而且 [ **發佈** ] 對話方塊會再次取得焦點。</span><span class="sxs-lookup"><span data-stu-id="ea867-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="ea867-146">系統會自動選取已建立的實例。</span><span class="sxs-lookup"><span data-stu-id="ea867-146">The instance that was created is automatically selected.</span></span>

![發佈對話方塊：選取 App Service 實例](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="ea867-148">此時，您必須將 API 新增至 Azure API 管理服務。</span><span class="sxs-lookup"><span data-stu-id="ea867-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="ea867-149">當您完成下列工作時，請讓 Visual Studio 保持開啟狀態。</span><span class="sxs-lookup"><span data-stu-id="ea867-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="ea867-150">將 API 新增至 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="ea867-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="ea867-151">開啟先前在 Azure 入口網站中建立的 API 管理服務實例。</span><span class="sxs-lookup"><span data-stu-id="ea867-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="ea867-152">選取 [ **api** ] 分頁：</span><span class="sxs-lookup"><span data-stu-id="ea867-152">Select the **APIs** blade:</span></span>

  ![從 API 管理服務實例選取的 Api blade](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="ea867-154">選取 [ **ECHO API** ] 旁邊的3個點，然後從快顯功能表中選取 [ **刪除** ]，將它移除。</span><span class="sxs-lookup"><span data-stu-id="ea867-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![從 API 管理服務實例刪除 echo API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="ea867-156">從 [ **新增 api** ] 面板中，選取 [ **空白 api** ] 磚：</span><span class="sxs-lookup"><span data-stu-id="ea867-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![顯示醒目提示空白 API 磚的畫面](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="ea867-158">在 [ **建立空白 API** ] 對話方塊中輸入下列值：</span><span class="sxs-lookup"><span data-stu-id="ea867-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="ea867-159">**顯示名稱**： *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="ea867-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="ea867-160">**名稱**： *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="ea867-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="ea867-161">**API Url 尾碼**： *v1*</span><span class="sxs-lookup"><span data-stu-id="ea867-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="ea867-162">將 [ **Web 服務 URL** ] 欄位保留空白。</span><span class="sxs-lookup"><span data-stu-id="ea867-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="ea867-163">選取 [建立] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ea867-163">Select the **Create** button.</span></span>

    ![[已完成的建立空白 api] 對話方塊的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="ea867-165">會建立空白 API。</span><span class="sxs-lookup"><span data-stu-id="ea867-165">The blank API is created.</span></span>

![API 管理入口網站中空白 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="ea867-167">將 ASP.NET Core web API 發佈至 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="ea867-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="ea867-168">切換回 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="ea867-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="ea867-169">[ **發行** ] 對話方塊應該仍在您之前離開的位置開啟。</span><span class="sxs-lookup"><span data-stu-id="ea867-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="ea867-170">選取新發佈的 Azure App Service，使其反白顯示。</span><span class="sxs-lookup"><span data-stu-id="ea867-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="ea867-171">選取 [下一步] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ea867-171">Select the **Next** button.</span></span>

    ![[發佈] 對話方塊的螢幕擷取畫面，其中已選取 app service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="ea867-173">對話方塊現在會顯示先前建立的 Azure API 管理服務。</span><span class="sxs-lookup"><span data-stu-id="ea867-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="ea867-174">展開它和 [ *api* ] 資料夾，以查看您所建立的空白 API。</span><span class="sxs-lookup"><span data-stu-id="ea867-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="ea867-175">選取空白 API 的名稱，然後選取 [ **完成]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ea867-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![在 [發行] 對話方塊中選取新建立的 Azure API 管理空白 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="ea867-177">對話方塊會隨即關閉，並顯示包含發佈相關資訊的摘要畫面。</span><span class="sxs-lookup"><span data-stu-id="ea867-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="ea867-178">選取 [發佈] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ea867-178">Select the **Publish** button.</span></span>

    ![顯示發行設定檔 Visual Studio 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="ea867-180">Web API 會同時發佈到 Azure App Service 和 Azure API 管理。</span><span class="sxs-lookup"><span data-stu-id="ea867-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="ea867-181">新的瀏覽器視窗隨即出現，並顯示在 Azure App Service 中執行的 API。</span><span class="sxs-lookup"><span data-stu-id="ea867-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="ea867-182">您可以關閉該視窗。</span><span class="sxs-lookup"><span data-stu-id="ea867-182">You can close that window.</span></span>

1. <span data-ttu-id="ea867-183">切換回 Azure 入口網站中的 Azure API 管理實例。</span><span class="sxs-lookup"><span data-stu-id="ea867-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="ea867-184">重新整理瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="ea867-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="ea867-185">選取您在先前步驟中建立的空白 API。</span><span class="sxs-lookup"><span data-stu-id="ea867-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="ea867-186">它現在已填入並可供您探索。</span><span class="sxs-lookup"><span data-stu-id="ea867-186">It's now populated and you can explore around.</span></span>

    ![Azure API 管理中已填入 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="ea867-188">設定已發佈的 API 名稱</span><span class="sxs-lookup"><span data-stu-id="ea867-188">Configure the published API name</span></span>

<span data-ttu-id="ea867-189">請注意，API 名稱與您命名的不同。</span><span class="sxs-lookup"><span data-stu-id="ea867-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="ea867-190">已發佈的 API 命名為 *WeatherAPI*;不過，您在建立時將它命名為 *WeatherForecasts* 。</span><span class="sxs-lookup"><span data-stu-id="ea867-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="ea867-191">請完成下列步驟來修正名稱：</span><span class="sxs-lookup"><span data-stu-id="ea867-191">Complete the following steps to fix the name:</span></span>

![醒目提示不同名稱的已填入 API 螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="ea867-193">從方法中刪除下行 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="ea867-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="ea867-194">將下列程式碼加入 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="ea867-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="ea867-195">開啟新建立的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="ea867-195">Open the newly created publish profile.</span></span> <span data-ttu-id="ea867-196">您可以在 *Properties/PublishProfiles* 資料夾的 **方案總管** 中找到它。</span><span class="sxs-lookup"><span data-stu-id="ea867-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![顯示反白顯示發行設定檔位置的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="ea867-198">將 `<OpenAPIDocumentName>` 元素的值從變更 `v1` 為 `WeatherForecasts` 。</span><span class="sxs-lookup"><span data-stu-id="ea867-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![發行設定檔所需變更的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="ea867-200">重新發佈 ASP.NET Core web API，並在 Azure 入口網站中開啟 Azure API 管理實例。</span><span class="sxs-lookup"><span data-stu-id="ea867-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="ea867-201">在您的瀏覽器中重新整理頁面。</span><span class="sxs-lookup"><span data-stu-id="ea867-201">Refresh the page in your browser.</span></span> <span data-ttu-id="ea867-202">您會看到 API 的名稱現在是正確的。</span><span class="sxs-lookup"><span data-stu-id="ea867-202">You'll see the name of the API is now correct.</span></span>

    ![入口網站中已完成 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="ea867-204">驗證 web API 是否正常運作</span><span class="sxs-lookup"><span data-stu-id="ea867-204">Verify the web API is working</span></span>

<span data-ttu-id="ea867-205">您可以使用下列步驟，從 Azure 入口網站在 Azure API 管理中測試已部署的 ASP.NET Core web API：</span><span class="sxs-lookup"><span data-stu-id="ea867-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="ea867-206">開啟 [測試] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ea867-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="ea867-207">選取 **/** 或 **取得** 作業。</span><span class="sxs-lookup"><span data-stu-id="ea867-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="ea867-208">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="ea867-208">Select **Send**.</span></span>

    ![測試前的入口網站螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="ea867-210">成功的回應看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="ea867-210">A successful response will look like the following:</span></span>

![API 管理成功回應的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="ea867-212">清除</span><span class="sxs-lookup"><span data-stu-id="ea867-212">Clean up</span></span>

<span data-ttu-id="ea867-213">當您完成測試應用程式時，請移至 [Azure 入口網站](https://portal.azure.com/) 並刪除應用程式。</span><span class="sxs-lookup"><span data-stu-id="ea867-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="ea867-214">選取 [資源群組]，然後選取您建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="ea867-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure 入口網站：資訊看板功能表中的 [資源群組]](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="ea867-216">在 [資源群組] 頁面中，選取 [刪除]。</span><span class="sxs-lookup"><span data-stu-id="ea867-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure 入口網站：[資源群組] 頁面](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="ea867-218">輸入資源群組的名稱，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="ea867-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="ea867-219">您在本教學課程中建立的應用程式和其他所有資源都會立即從 Azure 中刪除。</span><span class="sxs-lookup"><span data-stu-id="ea867-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ea867-220">下一步</span><span class="sxs-lookup"><span data-stu-id="ea867-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="ea867-221">其他資源</span><span class="sxs-lookup"><span data-stu-id="ea867-221">Additional resources</span></span>

- [<span data-ttu-id="ea867-222">Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="ea867-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="ea867-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea867-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
