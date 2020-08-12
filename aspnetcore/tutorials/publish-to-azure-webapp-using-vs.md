---
title: 使用 Visual Studio 將 ASP.NET Core 應用程式發行到 Azure
author: rick-anderson
description: 了解如何使用 Visual Studio 將 ASP.NET Core 應用程式發行到 Azure App Service。
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 1fced12700fcd5910c1484ebb9190c7652b2646e
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130700"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="a28ed-103">使用 Visual Studio 將 ASP.NET Core 應用程式發行到 Azure</span><span class="sxs-lookup"><span data-stu-id="a28ed-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="a28ed-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a28ed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="a28ed-105">如果您正在 macOS，請參閱[使用 Visual Studio for Mac 將 Web 應用程式發佈至 Azure App Service](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="a28ed-106">若要針對 App Service 部署問題進行疑難排解，請參閱 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="a28ed-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="a28ed-107">設定</span><span class="sxs-lookup"><span data-stu-id="a28ed-107">Set up</span></span>

* <span data-ttu-id="a28ed-108">如果您沒有帳戶，請開啟[免費的 Azure 帳戶](https://azure.microsoft.com/free/dotnet/)。</span><span class="sxs-lookup"><span data-stu-id="a28ed-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="a28ed-109">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="a28ed-109">Create a web app</span></span>

<span data-ttu-id="a28ed-110">在 Visual Studio 起始畫面中，選取 [檔案] > [新增] > [專案...]\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="a28ed-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![[檔案] 功能表](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="a28ed-112">完成 [新增專案]\*\*\*\* 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="a28ed-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="a28ed-113">選取 **ASP.NET Core Web 應用程式**。</span><span class="sxs-lookup"><span data-stu-id="a28ed-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a28ed-114">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-114">Select **Next**.</span></span>

![[新增專案] 對話方塊](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="a28ed-116">在 [新增 ASP.NET Core Web 應用程式]\*\*\*\* 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="a28ed-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="a28ed-117">選取 [ **Web 應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-117">Select **Web Application**.</span></span>
* <span data-ttu-id="a28ed-118">選取 [驗證] 底下的 [**變更**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-118">Select **Change** under Authentication.</span></span>

![新增 ASP.NET Core Web 對話方塊](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="a28ed-120">[變更驗證]\*\*\*\* 對話方塊會隨即出現。</span><span class="sxs-lookup"><span data-stu-id="a28ed-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="a28ed-121">選取 [個別使用者帳戶]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a28ed-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="a28ed-122">選取 **[確定]** 以返回**新的 ASP.NET Core Web 應用程式**，然後選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![[新增 ASP.NET Core Web 驗證] 對話方塊](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="a28ed-124">Visual Studio 會建立解決方案。</span><span class="sxs-lookup"><span data-stu-id="a28ed-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="a28ed-125">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="a28ed-125">Run the app</span></span>

* <span data-ttu-id="a28ed-126">按 CTRL+F5 執行專案。</span><span class="sxs-lookup"><span data-stu-id="a28ed-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="a28ed-127">測試**隱私權**連結。</span><span class="sxs-lookup"><span data-stu-id="a28ed-127">Test the **Privacy** link.</span></span>

![在 Microsoft Edge 中於 localhost 開啟的 Web 應用程式](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="a28ed-129">註冊使用者</span><span class="sxs-lookup"><span data-stu-id="a28ed-129">Register a user</span></span>

* <span data-ttu-id="a28ed-130">選取 [註冊]\*\*\*\* 並註冊新的使用者。</span><span class="sxs-lookup"><span data-stu-id="a28ed-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="a28ed-131">您可以使用虛構的電子郵件地址。</span><span class="sxs-lookup"><span data-stu-id="a28ed-131">You can use a fictitious email address.</span></span> <span data-ttu-id="a28ed-132">提交時，頁面會顯示下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="a28ed-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="a28ed-133">*「處理要求時資料庫作業失敗。應用程式資料庫內容的現有遷移可能會解決此問題。」*</span><span class="sxs-lookup"><span data-stu-id="a28ed-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="a28ed-134">選取 [套用移轉]\*\*\*\*，並在頁面更新後重新整理頁面。</span><span class="sxs-lookup"><span data-stu-id="a28ed-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![處理要求時資料庫作業失敗。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="a28ed-137">應用程式會顯示用來註冊新使用者和**登出**連結的電子郵件。</span><span class="sxs-lookup"><span data-stu-id="a28ed-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![在 Microsoft Edge 中開啟的 Web 應用程式。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="a28ed-140">將應用程式部署至 Azure</span><span class="sxs-lookup"><span data-stu-id="a28ed-140">Deploy the app to Azure</span></span>

<span data-ttu-id="a28ed-141">在方案總管中，以滑鼠右鍵按一下專案，然後選取 [發行]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a28ed-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![反白顯示 [發行] 連結的已開啟操作功能表](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="a28ed-143">在 [發行]\*\*\*\* 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="a28ed-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="a28ed-144">選取 [ **Azure**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-144">Select **Azure**.</span></span>
* <span data-ttu-id="a28ed-145">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-145">Select **Next**.</span></span>

![發佈對話方塊](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="a28ed-147">在 [發行]\*\*\*\* 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="a28ed-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="a28ed-148">選取\*\* (Linux) Azure App Service \*\*。</span><span class="sxs-lookup"><span data-stu-id="a28ed-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="a28ed-149">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-149">Select **Next**.</span></span>

![發佈對話方塊：選取 Azure 服務](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="a28ed-151">在 [**發行**] 對話方塊中，選取 [**建立新的 Azure App Service ...** ]</span><span class="sxs-lookup"><span data-stu-id="a28ed-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![發佈對話方塊：選取 Azure 服務實例](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="a28ed-153">[**建立 App Service** ] 對話方塊隨即出現：</span><span class="sxs-lookup"><span data-stu-id="a28ed-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="a28ed-154">會填入 [應用程式名稱]\*\*\*\*、[資源群組]\*\*\*\* 和 [App Service 方案]\*\*\*\* 輸入欄位。</span><span class="sxs-lookup"><span data-stu-id="a28ed-154">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="a28ed-155">您可以保留這些名稱，或變更它們。</span><span class="sxs-lookup"><span data-stu-id="a28ed-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="a28ed-156">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-156">Select **Create**.</span></span>

![建立 App Service 對話方塊](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="a28ed-158">建立完成後，會自動關閉對話方塊，而且 [**發行**] 對話方塊會再次取得焦點：</span><span class="sxs-lookup"><span data-stu-id="a28ed-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="a28ed-159">系統會自動選取剛建立的新實例。</span><span class="sxs-lookup"><span data-stu-id="a28ed-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="a28ed-160">選取 [完成]  。</span><span class="sxs-lookup"><span data-stu-id="a28ed-160">Select **Finish**.</span></span>

![發行對話方塊：選取 App Service 實例](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="a28ed-162">接下來，您會看到 [**發行設定檔摘要**] 頁面。</span><span class="sxs-lookup"><span data-stu-id="a28ed-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="a28ed-163">Visual Studio 偵測到此應用程式需要 SQL Server 資料庫，而且它會要求您進行設定。</span><span class="sxs-lookup"><span data-stu-id="a28ed-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="a28ed-164">選取 [設定] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-164">Select **Configure**.</span></span>

![發行設定檔摘要頁面：設定 SQL Server 相依性](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="a28ed-166">[**設定**相依性] 對話方塊隨即出現：</span><span class="sxs-lookup"><span data-stu-id="a28ed-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="a28ed-167">選取 [ **Azure SQL Database**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="a28ed-168">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-168">Select **Next**.</span></span>

![設定 SQL Server 相依性對話方塊](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="a28ed-170">在 [**設定 AZURE SQL database** ] 對話方塊中，選取 [**建立 SQL Database**</span><span class="sxs-lookup"><span data-stu-id="a28ed-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![[設定 Azure SQL Database] 對話方塊](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="a28ed-172">[**建立 Azure SQL Database**隨即出現：</span><span class="sxs-lookup"><span data-stu-id="a28ed-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="a28ed-173">[**資料庫名稱**]、[**資源群組**]、[**資料庫伺服器**] 和 [ **App Service 方案**專案] 欄位都會填入。</span><span class="sxs-lookup"><span data-stu-id="a28ed-173">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="a28ed-174">您可以保留這些值或加以變更。</span><span class="sxs-lookup"><span data-stu-id="a28ed-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="a28ed-175">輸入所選**資料庫伺服器**的**資料庫管理員使用者名稱**和**資料庫系統管理員密碼** (記下您使用的帳戶必須具有建立新 Azure SQL Database 的必要許可權) </span><span class="sxs-lookup"><span data-stu-id="a28ed-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="a28ed-176">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-176">Select **Create**.</span></span>

![新增 Azure SQL Database 對話方塊](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="a28ed-178">建立完成後，對話方塊會自動關閉，而且 [**設定 Azure SQL Database** ] 對話方塊會再次取得焦點：</span><span class="sxs-lookup"><span data-stu-id="a28ed-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="a28ed-179">系統會自動選取剛建立的新實例。</span><span class="sxs-lookup"><span data-stu-id="a28ed-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="a28ed-180">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-180">Select **Next**.</span></span>

![[設定 Azure SQL Database] 對話方塊](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="a28ed-182">在 [**設定 Azure SQL Database** ] 對話方塊的下一個步驟中：</span><span class="sxs-lookup"><span data-stu-id="a28ed-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="a28ed-183">輸入**資料庫連接的 [使用者名稱**] 和 [**資料庫連接密碼**] 欄位。</span><span class="sxs-lookup"><span data-stu-id="a28ed-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="a28ed-184">這些是您的應用程式將在執行時間用來連接到資料庫的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a28ed-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="a28ed-185">最佳做法是避免使用與上一個步驟中所使用的系統管理員使用者名稱 & 密碼相同的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a28ed-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="a28ed-186">選取 [完成]  。</span><span class="sxs-lookup"><span data-stu-id="a28ed-186">Select **Finish**.</span></span>

![設定 Azure SQL Database 對話方塊、連接字串詳細資料](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="a28ed-188">在 [**發行設定檔] 摘要**頁面中，選取 [**設定**]：</span><span class="sxs-lookup"><span data-stu-id="a28ed-188">In the **Publish Profile summary** page select **Settings**:</span></span>

![發行設定檔摘要頁面：編輯設定](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="a28ed-190">在 [發行]\*\*\*\* 對話方塊的 [設定]\*\*\*\* 頁面上：</span><span class="sxs-lookup"><span data-stu-id="a28ed-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="a28ed-191">展開 [資料庫]\*\*\*\* 並選取 [在執行階段使用此連接字串]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a28ed-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="a28ed-192">展開 [ **Entity Framework 遷移**]，然後選取 [**在發行時套用此遷移**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="a28ed-193">選取 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-193">Select **Save**.</span></span> <span data-ttu-id="a28ed-194">Visual Studio 會回到 [發行]\*\*\*\* 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="a28ed-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![[發行] 對話方塊：設定面板](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="a28ed-196">按一下 [發佈] 。</span><span class="sxs-lookup"><span data-stu-id="a28ed-196">Click **Publish**.</span></span> <span data-ttu-id="a28ed-197">Visual Studio 會將您的應用程式發佈至 Azure。</span><span class="sxs-lookup"><span data-stu-id="a28ed-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="a28ed-198">部署完成時，會在瀏覽器中開啟應用程式。</span><span class="sxs-lookup"><span data-stu-id="a28ed-198">When the deployment completes, the app is opened in a browser.</span></span>

![[發行] 對話方塊：設定面板](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="a28ed-200">更新應用程式</span><span class="sxs-lookup"><span data-stu-id="a28ed-200">Update the app</span></span>

* <span data-ttu-id="a28ed-201">編輯*Pages/Index. cshtml* Razor 頁面並變更其內容。</span><span class="sxs-lookup"><span data-stu-id="a28ed-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="a28ed-202">例如，您可以修改段落使其說出 "Hello ASP.NET Core!"：</span><span class="sxs-lookup"><span data-stu-id="a28ed-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="a28ed-203">再次選取 [發行**設定檔] 摘要**頁面中的 [**發佈**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![發行設定檔摘要頁面](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="a28ed-205">發行應用程式後，請驗證您進行的變更在 Azure 上有效。</span><span class="sxs-lookup"><span data-stu-id="a28ed-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![驗證工作是否完成](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="a28ed-207">清除</span><span class="sxs-lookup"><span data-stu-id="a28ed-207">Clean up</span></span>

<span data-ttu-id="a28ed-208">當您完成測試應用程式時，請移至 [Azure 入口網站](https://portal.azure.com/)並刪除應用程式。</span><span class="sxs-lookup"><span data-stu-id="a28ed-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="a28ed-209">選取 [資源群組]\*\*\*\*，然後選取您建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="a28ed-209">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure 入口網站：資訊看板功能表中的 [資源群組]](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="a28ed-211">在 [資源群組]\*\*\*\* 頁面中，選取 [刪除]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="a28ed-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure 入口網站：[資源群組] 頁面](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="a28ed-213">輸入資源群組的名稱，然後選取 [**刪除**]。</span><span class="sxs-lookup"><span data-stu-id="a28ed-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="a28ed-214">您在本教學課程中建立的應用程式和其他所有資源都會立即從 Azure 中刪除。</span><span class="sxs-lookup"><span data-stu-id="a28ed-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="a28ed-215">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a28ed-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="a28ed-216">其他資源</span><span class="sxs-lookup"><span data-stu-id="a28ed-216">Additional resources</span></span>

* <span data-ttu-id="a28ed-217">針對 Visual Studio Code，請參閱[發佈設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="a28ed-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="a28ed-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a28ed-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="a28ed-219">Azure 資源群組</span><span class="sxs-lookup"><span data-stu-id="a28ed-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="a28ed-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="a28ed-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
