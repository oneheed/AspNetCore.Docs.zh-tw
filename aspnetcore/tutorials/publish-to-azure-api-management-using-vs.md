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
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>使用 Visual Studio 將 ASP.NET Core web API 發佈至 Azure API 管理

依 [Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>設定

- 如果您沒有帳戶，請開啟[免費的 Azure 帳戶](https://azure.microsoft.com/free/dotnet/)。
- 如果尚未[建立新的 AZURE API 管理實例](/azure/api-management/get-started-create-service-instance)，請加以建立。
- [建立 WEB API 應用程式專案](xref:tutorials/first-web-api#create-a-web-project)。

## <a name="configure-the-app"></a>設定應用程式

將 Swagger 定義新增至 ASP.NET Core web API，可讓 Azure API 管理讀取應用程式的 API 定義。 完成下列步驟。

### <a name="add-swagger"></a>新增 Swagger

1. 將 [Swashbuckle AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 套件新增至 ASP.NET CORE web API 專案：

    ![設定 NuGet 對話方塊的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. 將下行新增至 `Startup.ConfigureServices` 方法：
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. 將下行新增至 `Startup.Configure` 方法：

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>變更 API 路由

接下來，您將變更存取動作所需的 URL 結構 `Get` `WeatherForecastController` 。 完成以下步驟：

1. 開啟 *WeatherForecastController.cs* 檔案。
1. 刪除 `[Route("[controller]")]` 類別層級屬性。 類別定義看起來會如下所示：

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. 將 `[Route("/")]` 屬性新增至 `Get()` 動作。 函式定義看起來會如下所示：

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>將 web API 發佈至 Azure App Service

請完成下列步驟，將 ASP.NET Core web API 發佈至 Azure API 管理：

1. 將 API 應用程式發佈至 Azure App Service。
1. 將空白 API 新增至 Azure API 管理服務實例。
1. 將 ASP.NET Core web API 應用程式發佈至 Azure API 管理服務實例。

### <a name="publish-the-api-app-to-azure-app-service"></a>將 API 應用程式發佈至 Azure App Service

請完成下列步驟，將 ASP.NET Core web API 發佈至 Azure API 管理：

1. 在 **方案總管**中，以滑鼠右鍵按一下專案，然後選取 [ **發行**]：

    ![反白顯示 [發行] 連結的已開啟操作功能表](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. 在 [ **發行** ] 對話方塊中，選取 [ **Azure** ]，然後選取 [ **下一步]** 按鈕：

    ![發佈對話方塊](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. 選取 **Azure App Service (Windows) ** ，然後選取 [ **下一步]** 按鈕：

    ![發佈對話方塊：選取 App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. 選取 [ **建立新的 Azure App Service**]。

    ![發佈對話方塊：選取 Azure 服務實例](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    [建立 App Service]**** 對話方塊隨即出現。 會填入 [應用程式名稱]****、[資源群組]**** 和 [App Service 方案]**** 輸入欄位。 您可以保留這些名稱，或變更它們。
1. 選取 [建立]**** 按鈕。

    ![建立 App Service 對話方塊](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

建立完成之後，對話方塊會自動關閉，而且 [ **發佈** ] 對話方塊會再次取得焦點。 系統會自動選取已建立的實例。

![發佈對話方塊：選取 App Service 實例](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

此時，您必須將 API 新增至 Azure API 管理服務。 當您完成下列工作時，請讓 Visual Studio 保持開啟狀態。

### <a name="add-an-api-to-azure-api-management"></a>將 API 新增至 Azure API 管理

1. 開啟先前在 Azure 入口網站中建立的 API 管理服務實例。 選取 [ **api** ] 分頁：

    ![從 API 管理服務實例選取的 Api blade](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. 從 [ **新增 api** ] 面板中，選取 [ **空白 api** ] 磚：

    ![顯示醒目提示空白 API 磚的畫面](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. 在 [ **建立空白 API** ] 對話方塊中輸入下列值：    

    - **顯示名稱**： *WeatherForecasts*
    - **名稱**： *weatherforecasts*
    - **API Url 尾碼**： *v1*
    - 將 [ **Web 服務 URL** ] 欄位保留空白。

1. 選取 [建立]**** 按鈕。

    ![[已完成的建立空白 api] 對話方塊的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

會建立空白 API。

![API 管理入口網站中空白 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>將 ASP.NET Core web API 發佈至 Azure API 管理

1. 切換回 Visual Studio。 [ **發行** ] 對話方塊應該仍在您之前離開的位置開啟。
1. 選取新發佈的 Azure App Service，使其反白顯示。
1. 選取 [下一步] 按鈕。

    ![[發佈] 對話方塊的螢幕擷取畫面，其中已選取 app service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. 對話方塊現在會顯示先前建立的 Azure API 管理服務。 展開它和 [ *api* ] 資料夾，以查看您所建立的空白 API。
1. 選取空白 API 的名稱，然後選取 [ **完成]** 按鈕。

    ![在 [發行] 對話方塊中選取新建立的 Azure API 管理空白 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. 對話方塊會隨即關閉，並顯示包含發佈相關資訊的摘要畫面。 選取 [發佈] 按鈕。

    ![顯示發行設定檔 Visual Studio 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Web API 會同時發佈到 Azure App Service 和 Azure API 管理。 新的瀏覽器視窗隨即出現，並顯示在 Azure App Service 中執行的 API。 您可以關閉該視窗。

1. 切換回 Azure 入口網站中的 Azure API 管理實例。
1. 重新整理瀏覽器視窗。
1. 選取您在先前步驟中建立的空白 API。 它現在已填入並可供您探索。

    ![Azure API 管理中已填入 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>設定已發佈的 API 名稱

請注意，API 名稱與您命名的不同。 已發佈的 API 命名為 *WeatherAPI*;不過，您在建立時將它命名為 *WeatherForecasts* 。 請完成下列步驟來修正名稱：

![醒目提示不同名稱的已填入 API 螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. 從方法中刪除下行 `Startup.ConfigureServices` ：
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. 將下列程式碼加入 `Startup.ConfigureServices` 方法：
    
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

1. 開啟新建立的發行設定檔。 您可以在*Properties/PublishProfiles*資料夾的**方案總管**中找到它。

    ![顯示反白顯示發行設定檔位置的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. 將 `<OpenAPIDocumentName>` 元素的值從變更 `v1` 為 `WeatherForecasts` 。

    ![發行設定檔所需變更的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. 重新發佈 ASP.NET Core web API，並在 Azure 入口網站中開啟 Azure API 管理實例。
1. 在您的瀏覽器中重新整理頁面。 您會看到 API 的名稱現在是正確的。

    ![入口網站中已完成 API 的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>驗證 web API 是否正常運作

您可以使用下列步驟，從 Azure 入口網站在 Azure API 管理中測試已部署的 ASP.NET Core web API：

1. 開啟 [測試]**** 索引標籤。
1. 選取 **/** 或 **取得** 作業。
1. 選取 [傳送]。

    ![測試前的入口網站螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

成功的回應看起來會如下所示：

![API 管理成功回應的螢幕擷取畫面](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>清理

當您完成測試應用程式時，請移至 [Azure 入口網站](https://portal.azure.com/) 並刪除應用程式。

1. 選取 [資源群組]****，然後選取您建立的資源群組。

    ![Azure 入口網站：資訊看板功能表中的 [資源群組]](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. 在 [資源群組]**** 頁面中，選取 [刪除]****。

    ![Azure 入口網站：[資源群組] 頁面](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. 輸入資源群組的名稱，然後選取 [ **刪除**]。 您在本教學課程中建立的應用程式和其他所有資源都會立即從 Azure 中刪除。

## <a name="next-steps"></a>後續步驟

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>其他資源

- [Azure API 管理](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
