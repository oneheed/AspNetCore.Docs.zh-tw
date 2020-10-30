---
title: 使用 ASP.NET Core 和 Azure 將應用程式部署至 App Service-DevOps
author: CamSoper
description: 將 ASP.NET Core 應用程式部署到 Azure App Service，這是使用 ASP.NET Core 和 Azure DevOps 的第一個步驟。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 52c4905ecb3a76f1dd10629f834b2b541b698774
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052353"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="bc378-103">將應用程式部署至 App Service</span><span class="sxs-lookup"><span data-stu-id="bc378-103">Deploy an app to App Service</span></span>

<span data-ttu-id="bc378-104">[Azure App Service](/azure/app-service/) 是 Azure 的 web 裝載平臺。</span><span class="sxs-lookup"><span data-stu-id="bc378-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="bc378-105">將 web 應用程式部署至 Azure App Service 可以手動方式或透過自動化程式來完成。</span><span class="sxs-lookup"><span data-stu-id="bc378-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="bc378-106">本指南的這一節會討論可以用手動方式或使用命令列的腳本觸發的部署方法，或是使用 Visual Studio 手動觸發的部署方法。</span><span class="sxs-lookup"><span data-stu-id="bc378-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="bc378-107">在本節中，您將完成下列工作：</span><span class="sxs-lookup"><span data-stu-id="bc378-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="bc378-108">下載並建立範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-108">Download and build the sample app.</span></span>
* <span data-ttu-id="bc378-109">使用 Azure Cloud Shell 建立 Azure App Service Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="bc378-110">使用 Git 將範例應用程式部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="bc378-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="bc378-111">使用 Visual Studio 部署應用程式的變更。</span><span class="sxs-lookup"><span data-stu-id="bc378-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="bc378-112">將預備位置新增至 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="bc378-113">將更新部署至預備位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="bc378-114">交換預備與生產位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="bc378-115">下載並測試應用程式</span><span class="sxs-lookup"><span data-stu-id="bc378-115">Download and test the app</span></span>

<span data-ttu-id="bc378-116">本指南中使用的應用程式是預先建立的 ASP.NET Core 應用程式、簡單的摘要 [讀取器](https://github.com/Azure-Samples/simple-feed-reader/)。</span><span class="sxs-lookup"><span data-stu-id="bc378-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="bc378-117">這是 :::no-loc(Razor)::: 頁面應用程式，會使用 `Microsoft.SyndicationFeed.ReaderWriter` API 來抓取 RSS/Atom 摘要，並在清單中顯示新聞專案。</span><span class="sxs-lookup"><span data-stu-id="bc378-117">It's a :::no-loc(Razor)::: Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="bc378-118">您可以隨意查看程式碼，但請務必瞭解此應用程式並沒有任何特殊之處。</span><span class="sxs-lookup"><span data-stu-id="bc378-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="bc378-119">這只是一個簡單的 ASP.NET Core 應用程式，以供說明之用。</span><span class="sxs-lookup"><span data-stu-id="bc378-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="bc378-120">從命令介面下載程式代碼、建立專案，然後如下所示執行。</span><span class="sxs-lookup"><span data-stu-id="bc378-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="bc378-121">*注意： Linux/macOS 使用者應該針對路徑進行適當的變更，例如，使用正斜線 (`/`) ，而不是反斜線 (`\`) 。*</span><span class="sxs-lookup"><span data-stu-id="bc378-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="bc378-122">將程式碼複製到本機電腦上的資料夾。</span><span class="sxs-lookup"><span data-stu-id="bc378-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="bc378-123">將您的工作資料夾變更為已建立的 *簡單摘要讀取器* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="bc378-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="bc378-124">還原套件，並建立方案。</span><span class="sxs-lookup"><span data-stu-id="bc378-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="bc378-125">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Dotnet run 命令成功](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="bc378-127">開啟瀏覽器並瀏覽至 `http://localhost:5000` 。</span><span class="sxs-lookup"><span data-stu-id="bc378-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="bc378-128">應用程式可讓您輸入或貼上新聞訂閱摘要 URL，以及查看新聞專案清單。</span><span class="sxs-lookup"><span data-stu-id="bc378-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![顯示 RSS 摘要內容的應用程式](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="bc378-130">當您滿意應用程式運作正常之後，請在命令列介面中按下 **Ctrl** + **C** 以關閉應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="bc378-131">建立 Azure App Service Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="bc378-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="bc378-132">若要部署應用程式，您必須建立 App Service [Web 應用程式](/azure/app-service/app-service-web-overview)。</span><span class="sxs-lookup"><span data-stu-id="bc378-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="bc378-133">建立 Web 應用程式之後，您會使用 Git 從本機電腦部署至該應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="bc378-134">登入 [Azure Cloud Shell](https://shell.azure.com/bash)。</span><span class="sxs-lookup"><span data-stu-id="bc378-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="bc378-135">注意：當您第一次登入時，Cloud Shell 會提示您建立設定檔的儲存體帳戶。</span><span class="sxs-lookup"><span data-stu-id="bc378-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="bc378-136">接受預設值或提供唯一的名稱。</span><span class="sxs-lookup"><span data-stu-id="bc378-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="bc378-137">使用 Cloud Shell 進行下列步驟。</span><span class="sxs-lookup"><span data-stu-id="bc378-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="bc378-138">a.</span><span class="sxs-lookup"><span data-stu-id="bc378-138">a.</span></span> <span data-ttu-id="bc378-139">宣告變數，以儲存您的 web 應用程式名稱。</span><span class="sxs-lookup"><span data-stu-id="bc378-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="bc378-140">名稱必須是唯一的，才能在預設 URL 中使用。</span><span class="sxs-lookup"><span data-stu-id="bc378-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="bc378-141">使用 Bash 函式 `$RANDOM` 來建立名稱，以保證唯一性和結果格式 `webappname99999` 。</span><span class="sxs-lookup"><span data-stu-id="bc378-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="bc378-142">b.</span><span class="sxs-lookup"><span data-stu-id="bc378-142">b.</span></span> <span data-ttu-id="bc378-143">建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="bc378-143">Create a resource group.</span></span> <span data-ttu-id="bc378-144">資源群組提供一個方法來匯總要以群組形式管理的 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="bc378-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="bc378-145">此命令會叫用 `az` [Azure CLI](/cli/azure/)。</span><span class="sxs-lookup"><span data-stu-id="bc378-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="bc378-146">CLI 可以在本機執行，但在 Cloud Shell 中使用它可節省時間和設定。</span><span class="sxs-lookup"><span data-stu-id="bc378-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="bc378-147">c.</span><span class="sxs-lookup"><span data-stu-id="bc378-147">c.</span></span> <span data-ttu-id="bc378-148">在 S1 層中建立 App Service 方案。</span><span class="sxs-lookup"><span data-stu-id="bc378-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="bc378-149">App Service 方案是共用相同定價層的 web apps 群組。</span><span class="sxs-lookup"><span data-stu-id="bc378-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="bc378-150">S1 層不是可用的，但它是預備位置功能的必要項。</span><span class="sxs-lookup"><span data-stu-id="bc378-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="bc378-151">d.</span><span class="sxs-lookup"><span data-stu-id="bc378-151">d.</span></span> <span data-ttu-id="bc378-152">使用相同資源群組中的 App Service 方案來建立 web 應用程式資源。</span><span class="sxs-lookup"><span data-stu-id="bc378-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="bc378-153">e。</span><span class="sxs-lookup"><span data-stu-id="bc378-153">e.</span></span> <span data-ttu-id="bc378-154">設定部署認證。</span><span class="sxs-lookup"><span data-stu-id="bc378-154">Set the deployment credentials.</span></span> <span data-ttu-id="bc378-155">這些部署認證適用于您訂用帳戶中的所有 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="bc378-156">請勿在使用者名稱中使用特殊字元。</span><span class="sxs-lookup"><span data-stu-id="bc378-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="bc378-157">f.</span><span class="sxs-lookup"><span data-stu-id="bc378-157">f.</span></span> <span data-ttu-id="bc378-158">將 web 應用程式設定為接受本機 Git 的部署，並顯示 *Git 部署 URL* 。</span><span class="sxs-lookup"><span data-stu-id="bc378-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL* .</span></span> <span data-ttu-id="bc378-159">**請注意此 URL 以供稍後參考** 。</span><span class="sxs-lookup"><span data-stu-id="bc378-159">**Note this URL for reference later** .</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="bc378-160">如</span><span class="sxs-lookup"><span data-stu-id="bc378-160">g.</span></span> <span data-ttu-id="bc378-161">顯示 *web 應用程式 URL* 。</span><span class="sxs-lookup"><span data-stu-id="bc378-161">Display the *web app URL* .</span></span> <span data-ttu-id="bc378-162">流覽至此 URL 以查看空白 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="bc378-163">**請注意此 URL 以供稍後參考** 。</span><span class="sxs-lookup"><span data-stu-id="bc378-163">**Note this URL for reference later** .</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="bc378-164">在本機電腦上使用命令 shell，流覽至 web 應用程式的專案資料夾 (例如 `.\simple-feed-reader\SimpleFeedReader`) 。</span><span class="sxs-lookup"><span data-stu-id="bc378-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="bc378-165">執行下列命令來設定 Git，以推送至部署 URL：</span><span class="sxs-lookup"><span data-stu-id="bc378-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="bc378-166">a.</span><span class="sxs-lookup"><span data-stu-id="bc378-166">a.</span></span> <span data-ttu-id="bc378-167">將遠端 URL 新增至本機存放庫。</span><span class="sxs-lookup"><span data-stu-id="bc378-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="bc378-168">b.</span><span class="sxs-lookup"><span data-stu-id="bc378-168">b.</span></span> <span data-ttu-id="bc378-169">將本機 *master* 分支推送至 *azure 生產* 遠端的 *主要* 分支。</span><span class="sxs-lookup"><span data-stu-id="bc378-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="bc378-170">系統會提示您輸入稍早建立的部署認證。</span><span class="sxs-lookup"><span data-stu-id="bc378-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="bc378-171">觀察命令 shell 中的輸出。</span><span class="sxs-lookup"><span data-stu-id="bc378-171">Observe the output in the command shell.</span></span> <span data-ttu-id="bc378-172">Azure 會從遠端建立 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="bc378-173">在瀏覽器中，流覽至 *Web 應用程式 URL* ，並注意已建立並部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="bc378-174">您可以使用將其他變更認可至本機 Git 存放庫 `git commit` 。</span><span class="sxs-lookup"><span data-stu-id="bc378-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="bc378-175">使用上述命令將這些變更推送至 Azure `git push` 。</span><span class="sxs-lookup"><span data-stu-id="bc378-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="bc378-176">使用 Visual Studio 部署</span><span class="sxs-lookup"><span data-stu-id="bc378-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="bc378-177">*注意：本節僅適用于 Windows。Linux 和 macOS 使用者應該進行下列步驟2所述的變更。儲存檔案，並使用將變更認可至本機存放庫 `git commit` 。最後，將變更推送至 `git push` ，如第一節所示。*</span><span class="sxs-lookup"><span data-stu-id="bc378-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="bc378-178">應用程式已從命令 shell 部署。</span><span class="sxs-lookup"><span data-stu-id="bc378-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="bc378-179">讓我們使用 Visual Studio 的整合式工具，將更新部署到應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="bc378-180">在幕後，Visual Studio 完成與命令列工具相同的工作，但是在 Visual Studio 的熟悉 UI 內。</span><span class="sxs-lookup"><span data-stu-id="bc378-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="bc378-181">在 Visual Studio 中開啟 *SimpleFeedReader .sln* 。</span><span class="sxs-lookup"><span data-stu-id="bc378-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="bc378-182">在方案總管中，開啟 *Pages\Index.cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="bc378-182">In Solution Explorer, open *Pages\Index.cshtml* .</span></span> <span data-ttu-id="bc378-183">將 `<h2>Simple Feed Reader</h2>` 變更為 `<h2>Simple Feed Reader - V2</h2>`。</span><span class="sxs-lookup"><span data-stu-id="bc378-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="bc378-184">按 **Ctrl** + **Shift** + **B** 來建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="bc378-185">在方案總管中，以滑鼠右鍵按一下專案，然後按一下 [ **發佈** ]。</span><span class="sxs-lookup"><span data-stu-id="bc378-185">In Solution Explorer, right-click on the project and click **Publish** .</span></span>

    ![顯示以滑鼠右鍵按一下、發佈的螢幕擷取畫面](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="bc378-187">Visual Studio 可以建立新的 App Service 資源，但此更新將會透過現有的部署發行。</span><span class="sxs-lookup"><span data-stu-id="bc378-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="bc378-188">在 [ **挑選發行目標** ] 對話方塊中，從左側清單中選取 **App Service** ，然後選取 [ **選取現有** 的]。</span><span class="sxs-lookup"><span data-stu-id="bc378-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing** .</span></span> <span data-ttu-id="bc378-189">按一下 [發佈] 。</span><span class="sxs-lookup"><span data-stu-id="bc378-189">Click **Publish** .</span></span>
6. <span data-ttu-id="bc378-190">在 [ **App Service** ] 對話方塊中，確認用來建立 Azure 訂用帳戶的 Microsoft 或組織帳戶顯示在右上角。</span><span class="sxs-lookup"><span data-stu-id="bc378-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="bc378-191">如果不是，請按一下下拉式清單並加以新增。</span><span class="sxs-lookup"><span data-stu-id="bc378-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="bc378-192">確認已選取正確的 Azure **訂** 用帳戶。</span><span class="sxs-lookup"><span data-stu-id="bc378-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="bc378-193">若要 **查看** ，請選取 **資源群組** 。</span><span class="sxs-lookup"><span data-stu-id="bc378-193">For **View** , select **Resource Group** .</span></span> <span data-ttu-id="bc378-194">展開 **AzureTutorial** 資源群組，然後選取現有的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="bc378-195">按一下 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="bc378-195">Click **OK** .</span></span>

    ![顯示 [發佈 App Service] 對話方塊的螢幕擷取畫面](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="bc378-197">Visual Studio 建立應用程式，並將其部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="bc378-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="bc378-198">流覽至 web 應用程式 URL。</span><span class="sxs-lookup"><span data-stu-id="bc378-198">Browse to the web app URL.</span></span> <span data-ttu-id="bc378-199">驗證 `<h2>` 元素修改是否為即時。</span><span class="sxs-lookup"><span data-stu-id="bc378-199">Validate that the `<h2>` element modification is live.</span></span>

![具有已變更標題的應用程式](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="bc378-201">部署位置</span><span class="sxs-lookup"><span data-stu-id="bc378-201">Deployment slots</span></span>

<span data-ttu-id="bc378-202">部署位置支援暫存變更，而不會影響在生產環境中執行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="bc378-203">當品質保證小組驗證應用程式的預備版本後，就可以交換生產和預備位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="bc378-204">以這種方式將預備環境中的應用程式升級至生產環境。</span><span class="sxs-lookup"><span data-stu-id="bc378-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="bc378-205">下列步驟會建立預備位置、對其部署一些變更，並在驗證之後將預備位置交換到生產環境。</span><span class="sxs-lookup"><span data-stu-id="bc378-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="bc378-206">登入 [Azure Cloud Shell](https://shell.azure.com/bash)（如果尚未登入）。</span><span class="sxs-lookup"><span data-stu-id="bc378-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="bc378-207">建立預備位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-207">Create the staging slot.</span></span>

    <span data-ttu-id="bc378-208">a.</span><span class="sxs-lookup"><span data-stu-id="bc378-208">a.</span></span> <span data-ttu-id="bc378-209">建立具有名稱 *暫存* 的部署位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-209">Create a deployment slot with the name *staging* .</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="bc378-210">b.</span><span class="sxs-lookup"><span data-stu-id="bc378-210">b.</span></span> <span data-ttu-id="bc378-211">將預備位置設定為使用本機 Git 的部署，並取得 **預備** 部署 URL。</span><span class="sxs-lookup"><span data-stu-id="bc378-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="bc378-212">**請注意此 URL 以供稍後參考** 。</span><span class="sxs-lookup"><span data-stu-id="bc378-212">**Note this URL for reference later** .</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="bc378-213">c.</span><span class="sxs-lookup"><span data-stu-id="bc378-213">c.</span></span> <span data-ttu-id="bc378-214">顯示預備位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="bc378-214">Display the staging slot's URL.</span></span> <span data-ttu-id="bc378-215">流覽至 URL 以查看空的預備位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="bc378-216">**請注意此 URL 以供稍後參考** 。</span><span class="sxs-lookup"><span data-stu-id="bc378-216">**Note this URL for reference later** .</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="bc378-217">在文字編輯器或 Visual Studio 中，再次修改 *Pages/Index. cshtml* ，讓 `<h2>` 元素讀取 `<h2>Simple Feed Reader - V3</h2>` 和儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="bc378-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="bc378-218">使用 Visual Studio 的 *Team Explorer* ] 索引標籤中的 [ **變更** ] 頁面，或使用本機電腦的命令 shell 輸入下列命令，將檔案認可至本機 Git 存放庫：</span><span class="sxs-lookup"><span data-stu-id="bc378-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="bc378-219">使用本機電腦的命令 shell，將預備部署 URL 新增為 Git 遠端，並推送認可的變更：</span><span class="sxs-lookup"><span data-stu-id="bc378-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="bc378-220">a.</span><span class="sxs-lookup"><span data-stu-id="bc378-220">a.</span></span> <span data-ttu-id="bc378-221">將預備環境的遠端 URL 新增至本機 Git 存放庫。</span><span class="sxs-lookup"><span data-stu-id="bc378-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="bc378-222">b.</span><span class="sxs-lookup"><span data-stu-id="bc378-222">b.</span></span> <span data-ttu-id="bc378-223">將本機 *master* 分支推送至 *azure 暫存* 的遠端 *主機* 分支。</span><span class="sxs-lookup"><span data-stu-id="bc378-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="bc378-224">等候 Azure 建立及部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="bc378-225">若要確認已將 V3 部署到預備位置，請開啟兩個瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="bc378-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="bc378-226">在一個視窗中，流覽至原始的 web 應用程式 URL。</span><span class="sxs-lookup"><span data-stu-id="bc378-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="bc378-227">在另一個視窗中，流覽至預備 web 應用程式 URL。</span><span class="sxs-lookup"><span data-stu-id="bc378-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="bc378-228">生產 URL 會提供應用程式 V2 的服務。</span><span class="sxs-lookup"><span data-stu-id="bc378-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="bc378-229">暫存 URL 是服務應用程式的 V3。</span><span class="sxs-lookup"><span data-stu-id="bc378-229">The staging URL serves V3 of the app.</span></span>

    ![比較瀏覽器視窗的螢幕擷取畫面](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="bc378-231">在 Cloud Shell 中，將已驗證/準備就緒預備位置交換到生產環境。</span><span class="sxs-lookup"><span data-stu-id="bc378-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="bc378-232">重新整理兩個瀏覽器視窗，確認是否發生交換。</span><span class="sxs-lookup"><span data-stu-id="bc378-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![在交換之後比較瀏覽器視窗](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="bc378-234">摘要</span><span class="sxs-lookup"><span data-stu-id="bc378-234">Summary</span></span>

<span data-ttu-id="bc378-235">本節將完成下列工作：</span><span class="sxs-lookup"><span data-stu-id="bc378-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="bc378-236">已下載並建立範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="bc378-237">使用 Azure Cloud Shell 建立 Azure App Service Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="bc378-238">使用 Git 將範例應用程式部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="bc378-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="bc378-239">使用 Visual Studio 部署應用程式的變更。</span><span class="sxs-lookup"><span data-stu-id="bc378-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="bc378-240">已將預備位置新增至 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bc378-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="bc378-241">已將更新部署至預備位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="bc378-242">交換預備和生產位置。</span><span class="sxs-lookup"><span data-stu-id="bc378-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="bc378-243">在下一節中，您將瞭解如何使用 Azure Pipelines 建立 DevOps 管線。</span><span class="sxs-lookup"><span data-stu-id="bc378-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="bc378-244">延伸閱讀</span><span class="sxs-lookup"><span data-stu-id="bc378-244">Additional reading</span></span>

* [<span data-ttu-id="bc378-245">Web 應用程式概觀</span><span class="sxs-lookup"><span data-stu-id="bc378-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="bc378-246">在 Azure App Service 中建置 .NET Core 和 SQL Database Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="bc378-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="bc378-247">設定 Azure App Service 的部署認證</span><span class="sxs-lookup"><span data-stu-id="bc378-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* <span data-ttu-id="bc378-248">[在 Azure App Service 中設定預備環境](/azure/app-service/web-sites-staged-publishing) \(部分機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="bc378-248">[Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing)</span></span>
