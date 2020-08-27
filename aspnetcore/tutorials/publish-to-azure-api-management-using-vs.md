---
title: 使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理
author: codemillmatt
description: 瞭解如何使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理。
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945665"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="d6923-103">使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="d6923-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="d6923-104">依 [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="d6923-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="d6923-105">設定</span><span class="sxs-lookup"><span data-stu-id="d6923-105">Set up</span></span>

- <span data-ttu-id="d6923-106">如果您沒有帳戶，請開啟[免費的 Azure 帳戶](https://azure.microsoft.com/free/dotnet/)。</span><span class="sxs-lookup"><span data-stu-id="d6923-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="d6923-107">如果尚未[建立新的 AZURE API 管理實例](/azure/api-management/get-started-create-service-instance)，請加以建立。</span><span class="sxs-lookup"><span data-stu-id="d6923-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="d6923-108">[建立 WEB API 應用程式專案](xref:tutorials/first-web-api#create-a-web-project)。</span><span class="sxs-lookup"><span data-stu-id="d6923-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="d6923-109">設定應用程式</span><span class="sxs-lookup"><span data-stu-id="d6923-109">Configure the app</span></span>

<span data-ttu-id="d6923-110">將 Swagger 定義新增至 ASP.NET Core web API，可讓 Azure API 管理讀取應用程式的 API 定義。</span><span class="sxs-lookup"><span data-stu-id="d6923-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="d6923-111">完成下列步驟。</span><span class="sxs-lookup"><span data-stu-id="d6923-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="d6923-112">新增 Swagger</span><span class="sxs-lookup"><span data-stu-id="d6923-112">Add Swagger</span></span>

1. <span data-ttu-id="d6923-113">將 [Swashbuckle AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 套件新增至 ASP.NET CORE web API 專案：</span><span class="sxs-lookup"><span data-stu-id="d6923-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![設定 NuGet 對話方塊的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="d6923-115">將下行新增至 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="d6923-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="d6923-116">將下行新增至 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="d6923-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="d6923-117">變更 API 路由</span><span class="sxs-lookup"><span data-stu-id="d6923-117">Change the API routing</span></span>

<span data-ttu-id="d6923-118">接下來，您將變更存取動作所需的 URL 結構 `Get` `WeatherForecastController` 。</span><span class="sxs-lookup"><span data-stu-id="d6923-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="d6923-119">完成以下步驟：</span><span class="sxs-lookup"><span data-stu-id="d6923-119">Complete the following steps:</span></span>

1. <span data-ttu-id="d6923-120">開啟 *WeatherForecastController.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="d6923-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="d6923-121">刪除 `[Route("[controller]")]` 類別層級屬性。</span><span class="sxs-lookup"><span data-stu-id="d6923-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="d6923-122">類別定義看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="d6923-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="d6923-123">將 `[Route("/")]` 屬性新增至 `Get()` 動作。</span><span class="sxs-lookup"><span data-stu-id="d6923-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="d6923-124">函式定義看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="d6923-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="d6923-125">將 web API 發佈至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6923-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="d6923-126">請完成下列步驟，將 ASP.NET Core web API 發佈至 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="d6923-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="d6923-127">將 API 應用程式發佈至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="d6923-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="d6923-128">將空白 API 新增至 Azure API 管理服務實例。</span><span class="sxs-lookup"><span data-stu-id="d6923-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="d6923-129">將 ASP.NET Core web API 應用程式發佈至 Azure API 管理服務實例。</span><span class="sxs-lookup"><span data-stu-id="d6923-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="d6923-130">將 API 應用程式發佈至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6923-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="d6923-131">請完成下列步驟，將 ASP.NET Core web API 發佈至 Azure API 管理：</span><span class="sxs-lookup"><span data-stu-id="d6923-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="d6923-132">在 **方案總管**中，以滑鼠右鍵按一下專案，然後選取 [ **發行**]：</span><span class="sxs-lookup"><span data-stu-id="d6923-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![反白顯示 [發行] 連結的已開啟操作功能表](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="d6923-134">在 [ **發行** ] 對話方塊中，選取 [ **Azure** ]，然後選取 [ **下一步]** 按鈕：</span><span class="sxs-lookup"><span data-stu-id="d6923-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![發佈對話方塊](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="d6923-136">選取 \*\*Azure App Service (Windows) \*\* ，然後選取 [ **下一步]** 按鈕：</span><span class="sxs-lookup"><span data-stu-id="d6923-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![發佈對話方塊：選取 App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="d6923-138">選取 [ **建立新的 Azure App Service**]。</span><span class="sxs-lookup"><span data-stu-id="d6923-138">Select **Create a new Azure App Service**.</span></span>

    ![發佈對話方塊：選取 Azure 服務實例](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="d6923-140">[建立 App Service]\*\*\*\* 對話方塊隨即出現。</span><span class="sxs-lookup"><span data-stu-id="d6923-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="d6923-141">會填入 [應用程式名稱]\*\*\*\*、[資源群組]\*\*\*\* 和 [App Service 方案]\*\*\*\* 輸入欄位。</span><span class="sxs-lookup"><span data-stu-id="d6923-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="d6923-142">您可以保留這些名稱，或變更它們。</span><span class="sxs-lookup"><span data-stu-id="d6923-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="d6923-143">選取 [建立]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d6923-143">Select the **Create** button.</span></span>

    ![建立 App Service 對話方塊](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="d6923-145">建立完成之後，對話方塊會自動關閉，而且 [ **發佈** ] 對話方塊會再次取得焦點。</span><span class="sxs-lookup"><span data-stu-id="d6923-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="d6923-146">系統會自動選取已建立的實例。</span><span class="sxs-lookup"><span data-stu-id="d6923-146">The instance that was created is automatically selected.</span></span>

![發佈對話方塊：選取 App Service 實例](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="d6923-148">此時，您必須將 API 新增至 Azure API 管理服務。</span><span class="sxs-lookup"><span data-stu-id="d6923-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="d6923-149">當您完成下列工作時，請讓 Visual Studio 保持開啟狀態。</span><span class="sxs-lookup"><span data-stu-id="d6923-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="d6923-150">將 API 新增至 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="d6923-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="d6923-151">開啟先前在 Azure 入口網站中建立的 API 管理服務實例。</span><span class="sxs-lookup"><span data-stu-id="d6923-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="d6923-152">選取 [ **api** ] 分頁：</span><span class="sxs-lookup"><span data-stu-id="d6923-152">Select the **APIs** blade:</span></span>

    ![從 API 管理服務實例選取的 Api blade](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="d6923-154">從 [ **新增 api** ] 面板中，選取 [ **空白 api** ] 磚：</span><span class="sxs-lookup"><span data-stu-id="d6923-154">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

    ![顯示醒目提示空白 API 磚的畫面](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="d6923-156">在 [ **建立空白 API** ] 對話方塊中輸入下列值：</span><span class="sxs-lookup"><span data-stu-id="d6923-156">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="d6923-157">**顯示名稱**： *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="d6923-157">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="d6923-158">**名稱**： *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="d6923-158">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="d6923-159">**API Url 尾碼**： *v1*</span><span class="sxs-lookup"><span data-stu-id="d6923-159">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="d6923-160">將 [ **Web 服務 URL** ] 欄位保留空白。</span><span class="sxs-lookup"><span data-stu-id="d6923-160">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="d6923-161">選取 [建立]\*\*\*\* 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d6923-161">Select the **Create** button.</span></span>

    ![[已完成的建立空白 api] 對話方塊的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="d6923-163">會建立空白 API。</span><span class="sxs-lookup"><span data-stu-id="d6923-163">The blank API is created.</span></span>

![API 管理入口網站中空白 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="d6923-165">將 ASP.NET Core web API 發佈至 Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="d6923-165">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="d6923-166">切換回 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="d6923-166">Switch back to Visual Studio.</span></span> <span data-ttu-id="d6923-167">[ **發行** ] 對話方塊應該仍在您之前離開的位置開啟。</span><span class="sxs-lookup"><span data-stu-id="d6923-167">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="d6923-168">選取新發佈的 Azure App Service，使其反白顯示。</span><span class="sxs-lookup"><span data-stu-id="d6923-168">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="d6923-169">選取 [下一步] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d6923-169">Select the **Next** button.</span></span>

    ![[發佈] 對話方塊的螢幕擷取畫面，其中已選取 app service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="d6923-171">對話方塊現在會顯示先前建立的 Azure API 管理服務。</span><span class="sxs-lookup"><span data-stu-id="d6923-171">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="d6923-172">展開它和 [ *api* ] 資料夾，以查看您所建立的空白 API。</span><span class="sxs-lookup"><span data-stu-id="d6923-172">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="d6923-173">選取空白 API 的名稱，然後選取 [ **完成]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d6923-173">Select the blank API's name and select the **Finish** button.</span></span>

    ![在 [發行] 對話方塊中選取新建立的 Azure API 管理空白 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="d6923-175">對話方塊會隨即關閉，並顯示包含發佈相關資訊的摘要畫面。</span><span class="sxs-lookup"><span data-stu-id="d6923-175">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="d6923-176">選取 [發佈] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d6923-176">Select the **Publish** button.</span></span>

    ![顯示發行設定檔 Visual Studio 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="d6923-178">Web API 會同時發佈到 Azure App Service 和 Azure API 管理。</span><span class="sxs-lookup"><span data-stu-id="d6923-178">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="d6923-179">新的瀏覽器視窗隨即出現，並顯示在 Azure App Service 中執行的 API。</span><span class="sxs-lookup"><span data-stu-id="d6923-179">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="d6923-180">您可以關閉該視窗。</span><span class="sxs-lookup"><span data-stu-id="d6923-180">You can close that window.</span></span>

1. <span data-ttu-id="d6923-181">切換回 Azure 入口網站中的 Azure API 管理實例。</span><span class="sxs-lookup"><span data-stu-id="d6923-181">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="d6923-182">重新整理瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="d6923-182">Refresh the browser window.</span></span>
1. <span data-ttu-id="d6923-183">選取您在先前步驟中建立的空白 API。</span><span class="sxs-lookup"><span data-stu-id="d6923-183">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="d6923-184">它現在已填入並可供您探索。</span><span class="sxs-lookup"><span data-stu-id="d6923-184">It's now populated and you can explore around.</span></span>

    ![Azure API 管理中已填入 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="d6923-186">設定已發佈的 API 名稱</span><span class="sxs-lookup"><span data-stu-id="d6923-186">Configure the published API name</span></span>

<span data-ttu-id="d6923-187">請注意，API 名稱與您命名的不同。</span><span class="sxs-lookup"><span data-stu-id="d6923-187">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="d6923-188">已發佈的 API 命名為 *WeatherAPI*;不過，您在建立時將它命名為 *WeatherForecasts* 。</span><span class="sxs-lookup"><span data-stu-id="d6923-188">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="d6923-189">請完成下列步驟來修正名稱：</span><span class="sxs-lookup"><span data-stu-id="d6923-189">Complete the following steps to fix the name:</span></span>

![醒目提示不同名稱的已填入 API 螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="d6923-191">從方法中刪除下行 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d6923-191">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="d6923-192">將下列程式碼加入 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="d6923-192">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="d6923-193">開啟新建立的發行設定檔。</span><span class="sxs-lookup"><span data-stu-id="d6923-193">Open the newly created publish profile.</span></span> <span data-ttu-id="d6923-194">您可以在*Properties/PublishProfiles*資料夾的**方案總管**中找到它。</span><span class="sxs-lookup"><span data-stu-id="d6923-194">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![顯示反白顯示發行設定檔位置的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="d6923-196">將 `<OpenAPIDocumentName>` 元素的值從變更 `v1` 為 `WeatherForecasts` 。</span><span class="sxs-lookup"><span data-stu-id="d6923-196">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![發行設定檔所需變更的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="d6923-198">重新發佈 ASP.NET Core web API，並在 Azure 入口網站中開啟 Azure API 管理實例。</span><span class="sxs-lookup"><span data-stu-id="d6923-198">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="d6923-199">在您的瀏覽器中重新整理頁面。</span><span class="sxs-lookup"><span data-stu-id="d6923-199">Refresh the page in your browser.</span></span> <span data-ttu-id="d6923-200">您會看到 API 的名稱現在是正確的。</span><span class="sxs-lookup"><span data-stu-id="d6923-200">You'll see the name of the API is now correct.</span></span>

    ![入口網站中已完成 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="d6923-202">驗證 web API 是否正常運作</span><span class="sxs-lookup"><span data-stu-id="d6923-202">Verify the web API is working</span></span>

<span data-ttu-id="d6923-203">您可以使用下列步驟，從 Azure 入口網站在 Azure API 管理中測試已部署的 ASP.NET Core web API：</span><span class="sxs-lookup"><span data-stu-id="d6923-203">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="d6923-204">開啟 [測試]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="d6923-204">Open the **Test** tab.</span></span>
1. <span data-ttu-id="d6923-205">選取 **/** 或 **取得** 作業。</span><span class="sxs-lookup"><span data-stu-id="d6923-205">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="d6923-206">選取 [傳送]。</span><span class="sxs-lookup"><span data-stu-id="d6923-206">Select **Send**.</span></span>

    ![測試前的入口網站螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="d6923-208">成功的回應看起來會如下所示：</span><span class="sxs-lookup"><span data-stu-id="d6923-208">A successful response will look like the following:</span></span>

![API 管理成功回應的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="d6923-210">清理</span><span class="sxs-lookup"><span data-stu-id="d6923-210">Clean up</span></span>

<span data-ttu-id="d6923-211">當您完成測試應用程式時，請移至 [Azure 入口網站](https://portal.azure.com/) 並刪除應用程式。</span><span class="sxs-lookup"><span data-stu-id="d6923-211">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="d6923-212">選取 [資源群組]\*\*\*\*，然後選取您建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="d6923-212">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure 入口網站：資訊看板功能表中的 [資源群組]](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="d6923-214">在 [資源群組]\*\*\*\* 頁面中，選取 [刪除]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="d6923-214">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure 入口網站：[資源群組] 頁面](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="d6923-216">輸入資源群組的名稱，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="d6923-216">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="d6923-217">您在本教學課程中建立的應用程式和其他所有資源都會立即從 Azure 中刪除。</span><span class="sxs-lookup"><span data-stu-id="d6923-217">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d6923-218">後續步驟</span><span class="sxs-lookup"><span data-stu-id="d6923-218">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="d6923-219">其他資源</span><span class="sxs-lookup"><span data-stu-id="d6923-219">Additional resources</span></span>

- [<span data-ttu-id="d6923-220">Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="d6923-220">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="d6923-221">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6923-221">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
