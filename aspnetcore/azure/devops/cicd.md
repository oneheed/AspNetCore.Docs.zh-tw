---
title: 持續整合和部署-使用 ASP.NET Core 和 Azure DevOps
author: CamSoper
description: 使用 ASP.NET Core 和 Azure DevOps 中的持續整合和部署
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 18b2c6ce27132844402f88b2817a07e3588d81c1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586263"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="b89e5-103">持續整合與部署</span><span class="sxs-lookup"><span data-stu-id="b89e5-103">Continuous integration and deployment</span></span>

<span data-ttu-id="b89e5-104">在上一章中，您已建立簡單摘要讀取器應用程式的本機 Git 存放庫。</span><span class="sxs-lookup"><span data-stu-id="b89e5-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="b89e5-105">在本章中，您會將該程式碼發佈至 GitHub 存放庫，並使用 Azure 管線來建立 Azure DevOps Services 管線。</span><span class="sxs-lookup"><span data-stu-id="b89e5-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="b89e5-106">管線可讓應用程式持續組建和部署。</span><span class="sxs-lookup"><span data-stu-id="b89e5-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="b89e5-107">任何對 GitHub 存放庫的認可都會觸發組建，以及部署至 Azure Web 應用程式的預備位置。</span><span class="sxs-lookup"><span data-stu-id="b89e5-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="b89e5-108">在本節中，您將完成下列工作：</span><span class="sxs-lookup"><span data-stu-id="b89e5-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="b89e5-109">將應用程式的程式碼發佈至 GitHub</span><span class="sxs-lookup"><span data-stu-id="b89e5-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="b89e5-110">中斷本機 Git 部署的連線</span><span class="sxs-lookup"><span data-stu-id="b89e5-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="b89e5-111">建立 Azure DevOps 組織</span><span class="sxs-lookup"><span data-stu-id="b89e5-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="b89e5-112">在 Azure DevOps Services 中建立 team 專案</span><span class="sxs-lookup"><span data-stu-id="b89e5-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="b89e5-113">建立組建定義</span><span class="sxs-lookup"><span data-stu-id="b89e5-113">Create a build definition</span></span>
* <span data-ttu-id="b89e5-114">建立發行管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-114">Create a release pipeline</span></span>
* <span data-ttu-id="b89e5-115">將變更認可至 GitHub 並自動部署至 Azure</span><span class="sxs-lookup"><span data-stu-id="b89e5-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="b89e5-116">檢查 Azure 管線管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="b89e5-117">將應用程式的程式碼發佈至 GitHub</span><span class="sxs-lookup"><span data-stu-id="b89e5-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="b89e5-118">開啟瀏覽器視窗，並流覽至 `https://github.com` 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="b89e5-119">按一下 **+** 標頭中的下拉式清單，然後選取 [ **新增存放庫**]：</span><span class="sxs-lookup"><span data-stu-id="b89e5-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub 新的存放庫選項](media/cicd/github-new-repo.png)

1. <span data-ttu-id="b89e5-121">在 [**擁有** 者] 下拉式清單中選取您的帳戶，然後在 [存放 **庫名稱**] 文字方塊中輸入 *簡單摘要讀取程式*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="b89e5-122">按一下 [ **建立存放庫** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="b89e5-123">開啟您本機電腦的命令 shell。</span><span class="sxs-lookup"><span data-stu-id="b89e5-123">Open your local machine's command shell.</span></span> <span data-ttu-id="b89e5-124">流覽至儲存 *簡單摘要讀取器* Git 儲存機制的目錄。</span><span class="sxs-lookup"><span data-stu-id="b89e5-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="b89e5-125">將現有的 *來源* 遠端重新命名為 *上游*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="b89e5-126">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b89e5-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="b89e5-127">新增指向 GitHub 上存放庫複本的新 *原始來源* 遠端。</span><span class="sxs-lookup"><span data-stu-id="b89e5-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="b89e5-128">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b89e5-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="b89e5-129">將您的本機 Git 存放庫發佈到新建立的 GitHub 存放庫。</span><span class="sxs-lookup"><span data-stu-id="b89e5-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="b89e5-130">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b89e5-130">Execute the following command:</span></span>

    ```console
    git push -u origin main
    ```

1. <span data-ttu-id="b89e5-131">開啟瀏覽器視窗，並流覽至 `https://github.com/<GitHub_username>/simple-feed-reader/` 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="b89e5-132">驗證您的程式碼出現在 GitHub 存放庫中。</span><span class="sxs-lookup"><span data-stu-id="b89e5-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="b89e5-133">中斷本機 Git 部署的連線</span><span class="sxs-lookup"><span data-stu-id="b89e5-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="b89e5-134">使用下列步驟移除本機 Git 部署。</span><span class="sxs-lookup"><span data-stu-id="b89e5-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="b89e5-135"> (Azure DevOps 服務的 azure 管線) 都取代並擴充該功能。</span><span class="sxs-lookup"><span data-stu-id="b89e5-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="b89e5-136">開啟 [Azure 入口網站](https://portal.azure.com/)，然後流覽至 *預備環境 (mywebapp \<unique_number\> /Staging)* Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b89e5-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="b89e5-137">在入口網站的 [搜尋] 方塊中輸入 *預備* 環境，即可快速找出 Web 應用程式：</span><span class="sxs-lookup"><span data-stu-id="b89e5-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![預備 Web 應用程式搜尋詞彙](media/cicd/portal-search-box.png)

1. <span data-ttu-id="b89e5-139">按一下 [ **部署中心**]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-139">Click **Deployment Center**.</span></span> <span data-ttu-id="b89e5-140">新的面板隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-140">A new panel appears.</span></span> <span data-ttu-id="b89e5-141">按一下 **[中斷連線]** ，移除上一章中新增的本機 Git 原始檔控制設定。</span><span class="sxs-lookup"><span data-stu-id="b89e5-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="b89e5-142">按一下 [ **是]** 按鈕，確認移除操作。</span><span class="sxs-lookup"><span data-stu-id="b89e5-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="b89e5-143">流覽至 *mywebapp<unique_number>* App Service。</span><span class="sxs-lookup"><span data-stu-id="b89e5-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="b89e5-144">提醒您，入口網站的搜尋方塊可以用來快速找出 App Service。</span><span class="sxs-lookup"><span data-stu-id="b89e5-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="b89e5-145">按一下 [ **部署中心**]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-145">Click **Deployment Center**.</span></span> <span data-ttu-id="b89e5-146">新的面板隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-146">A new panel appears.</span></span> <span data-ttu-id="b89e5-147">按一下 **[中斷連線]** ，移除上一章中新增的本機 Git 原始檔控制設定。</span><span class="sxs-lookup"><span data-stu-id="b89e5-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="b89e5-148">按一下 [ **是]** 按鈕，確認移除操作。</span><span class="sxs-lookup"><span data-stu-id="b89e5-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="b89e5-149">建立 Azure DevOps 組織</span><span class="sxs-lookup"><span data-stu-id="b89e5-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="b89e5-150">開啟瀏覽器，然後流覽至 [ [Azure DevOps 組織建立] 頁面](https://go.microsoft.com/fwlink/?LinkId=307137)。</span><span class="sxs-lookup"><span data-stu-id="b89e5-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="b89e5-151">在 [ **挑選易記名稱** ] 文字方塊中輸入唯一的名稱，以形成用來存取 Azure DevOps 組織的 URL。</span><span class="sxs-lookup"><span data-stu-id="b89e5-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="b89e5-152">選取 **Git** 選項按鈕，因為程式碼裝載于 GitHub 存放庫中。</span><span class="sxs-lookup"><span data-stu-id="b89e5-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="b89e5-153">按一下 [繼續] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-153">Click the **Continue** button.</span></span> <span data-ttu-id="b89e5-154">短暫等候之後，就會建立名為 *MyFirstProject* 的帳戶和 team 專案。</span><span class="sxs-lookup"><span data-stu-id="b89e5-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps 組織建立頁面](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="b89e5-156">開啟確認電子郵件，指出 Azure DevOps 組織和專案已準備好可供使用。</span><span class="sxs-lookup"><span data-stu-id="b89e5-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="b89e5-157">按一下 [ **啟動您的專案** ] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="b89e5-157">Click the **Start your project** button:</span></span>

    ![啟動專案按鈕](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="b89e5-159">瀏覽器會開啟 *\<account_name\> visualstudio.com*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="b89e5-160">按一下 [ *MyFirstProject* ] 連結，開始設定專案的 DevOps 管線。</span><span class="sxs-lookup"><span data-stu-id="b89e5-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="b89e5-161">設定 Azure 管線管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="b89e5-162">完成三個不同的步驟。</span><span class="sxs-lookup"><span data-stu-id="b89e5-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="b89e5-163">完成下列三個區段中的步驟，會導致操作 DevOps 管線。</span><span class="sxs-lookup"><span data-stu-id="b89e5-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="b89e5-164">將 GitHub 存放庫的存取權授與 Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="b89e5-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="b89e5-165">從外部存放庫的 [可折疊] 展開 **或建立程式碼** 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="b89e5-166">按一下 [ **安裝組建** ] 按鈕：</span><span class="sxs-lookup"><span data-stu-id="b89e5-166">Click the **Setup Build** button:</span></span>

    ![設定組建按鈕](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="b89e5-168">從 [**選取來源**] 區段中選取 **GitHub** 選項：</span><span class="sxs-lookup"><span data-stu-id="b89e5-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![選取來源-GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="b89e5-170">需要授權，Azure DevOps 才能存取您的 GitHub 存放庫。</span><span class="sxs-lookup"><span data-stu-id="b89e5-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="b89e5-171">在 [連線 **名稱**] 文字方塊中，輸入 *<GitHub_username> GitHub 連接*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="b89e5-172">例如：</span><span class="sxs-lookup"><span data-stu-id="b89e5-172">For example:</span></span>

    ![GitHub 連接名稱](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="b89e5-174">如果您的 GitHub 帳戶已啟用雙因素驗證，則需要個人存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b89e5-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="b89e5-175">在此情況下，請按一下 [ **使用 GitHub 個人存取權杖的授權** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="b89e5-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="b89e5-176">如需協助，請參閱 [官方 GitHub 個人存取權杖的建立指示](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="b89e5-177">只需要許可權的存放 *庫範圍。*</span><span class="sxs-lookup"><span data-stu-id="b89e5-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="b89e5-178">否則，請按一下 [ **使用 OAuth 授權** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="b89e5-179">出現提示時，請登入您的 GitHub 帳戶。</span><span class="sxs-lookup"><span data-stu-id="b89e5-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="b89e5-180">然後選取 [授權]，以授與 Azure DevOps 組織的存取權。</span><span class="sxs-lookup"><span data-stu-id="b89e5-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="b89e5-181">如果成功，就會建立新的服務端點。</span><span class="sxs-lookup"><span data-stu-id="b89e5-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="b89e5-182">按一下 [存放 **庫** ] 按鈕旁的省略號按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="b89e5-183">從清單中選取 *<GitHub_username>/simple-feed-reader* 存放庫。</span><span class="sxs-lookup"><span data-stu-id="b89e5-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="b89e5-184">按一下 [選取] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="b89e5-185">從 [**手動和排程的組建**] 下拉式清單中，選取預設分支 (*主要*) 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-185">Select the default branch (*main*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="b89e5-186">按一下 [繼續] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-186">Click the **Continue** button.</span></span> <span data-ttu-id="b89e5-187">[範本選取] 頁面隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="b89e5-188">建立組建定義</span><span class="sxs-lookup"><span data-stu-id="b89e5-188">Create the build definition</span></span>

1. <span data-ttu-id="b89e5-189">在 [範本選擇] 頁面的 [搜尋] 方塊中，輸入 *ASP.NET Core* ：</span><span class="sxs-lookup"><span data-stu-id="b89e5-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![在範本頁面上 ASP.NET 核心搜尋](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="b89e5-191">範本搜尋結果隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-191">The template search results appear.</span></span> <span data-ttu-id="b89e5-192">將滑鼠停留在 **ASP.NET Core** 範本上方，然後按一下 [套用 **] 按鈕。**</span><span class="sxs-lookup"><span data-stu-id="b89e5-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="b89e5-193">組建 **定義的 [工作]** 索引標籤隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="b89e5-194">按一下 [ **觸發** 程式] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b89e5-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="b89e5-195">勾選 [ **啟用持續整合** ] 方塊。</span><span class="sxs-lookup"><span data-stu-id="b89e5-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="b89e5-196">在 [ **分支篩選** ] 區段下，確認 [ **類型** ] 下拉式清單已設定為 [ *包含*]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="b89e5-197">將 [ **分支規格** ] 下拉式清單設定為 [ *主要*]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-197">Set the **Branch specification** drop-down to *main*.</span></span>

    ![啟用持續整合設定](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="b89e5-199">這些設定會在將任何變更推送至 GitHub 存放庫 (*主要*) 的預設分支時，造成組建觸發程式。</span><span class="sxs-lookup"><span data-stu-id="b89e5-199">These settings cause a build to trigger when any change is pushed to the default branch (*main*) of the GitHub repository.</span></span> <span data-ttu-id="b89e5-200">持續整合會在將 [變更認可至 GitHub 並自動部署至 Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) 區段中進行測試。</span><span class="sxs-lookup"><span data-stu-id="b89e5-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="b89e5-201">按一下 [ **儲存 & 佇列** ] 按鈕，然後選取 [ **儲存** ] 選項：</span><span class="sxs-lookup"><span data-stu-id="b89e5-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![[儲存] 按鈕](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="b89e5-203">下列強制回應對話方塊隨即出現：</span><span class="sxs-lookup"><span data-stu-id="b89e5-203">The following modal dialog appears:</span></span>

    ![儲存組建定義-強制回應對話方塊](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="b89e5-205">使用的預設資料夾 *\\* ，然後按一下 [ **儲存** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="b89e5-206">建立發行管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-206">Create the release pipeline</span></span>

1. <span data-ttu-id="b89e5-207">按一下 team 專案的 [ **發行** ] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b89e5-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="b89e5-208">按一下 [ **新增管線** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-208">Click the **New pipeline** button.</span></span>

    ![[發行] 索引標籤-[新增定義] 按鈕](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="b89e5-210">[範本選取範圍] 窗格隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="b89e5-211">在 [範本選擇] 頁面的 [搜尋] 方塊中，輸入 *App Service* ：</span><span class="sxs-lookup"><span data-stu-id="b89e5-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![發行管線範本搜尋方塊](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="b89e5-213">範本搜尋結果隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-213">The template search results appear.</span></span> <span data-ttu-id="b89e5-214">將滑鼠停留在具有位置範本的 **Azure App Service 部署** 上，然後按一下 [套用 **] 按鈕。**</span><span class="sxs-lookup"><span data-stu-id="b89e5-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="b89e5-215">發行管線的 [ **管線** ] 索引標籤隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![發行管線管線索引標籤](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="b89e5-217">按一下 [成品] 方塊中 **的 [** **加入**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="b89e5-218">[ **新增** 成品] 面板隨即出現：</span><span class="sxs-lookup"><span data-stu-id="b89e5-218">The **Add artifact** panel appears:</span></span>

    ![發行管線-新增成品面板](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="b89e5-220">從 [**來源類型**] 區段中選取 [**組建**] 磚。</span><span class="sxs-lookup"><span data-stu-id="b89e5-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="b89e5-221">此類型可讓您將發行管線連結至組建定義。</span><span class="sxs-lookup"><span data-stu-id="b89e5-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="b89e5-222">從 [**專案**] 下拉式清單中選取 [ *MyFirstProject* ]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="b89e5-223">從 [**來源 (組建定義])** 下拉式清單中，選取組建定義名稱 *MYFIRSTPROJECT-ASP.NET Core-CI*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="b89e5-224">從 **預設版本** 下拉式清單中選取 [*最新*]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="b89e5-225">此選項會建立最新的組建定義執行所產生的構件。</span><span class="sxs-lookup"><span data-stu-id="b89e5-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="b89e5-226">以 *Drop* 取代 [**來源別名**] 文字方塊中的文字。</span><span class="sxs-lookup"><span data-stu-id="b89e5-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="b89e5-227">按一下 [新增]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-227">Click the **Add** button.</span></span> <span data-ttu-id="b89e5-228">[ **構件** ] 區段會更新以顯示變更。</span><span class="sxs-lookup"><span data-stu-id="b89e5-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="b89e5-229">按一下閃電圖示，以啟用持續部署：</span><span class="sxs-lookup"><span data-stu-id="b89e5-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![發行管線構件-閃電圖示](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="b89e5-231">啟用此選項後，每次有新的組建時，就會進行部署。</span><span class="sxs-lookup"><span data-stu-id="b89e5-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="b89e5-232">[ **持續部署觸發** 程式] 面板會顯示在右側。</span><span class="sxs-lookup"><span data-stu-id="b89e5-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="b89e5-233">按一下切換按鈕以啟用此功能。</span><span class="sxs-lookup"><span data-stu-id="b89e5-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="b89e5-234">不需要啟用 **提取要求觸發** 程式。</span><span class="sxs-lookup"><span data-stu-id="b89e5-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="b89e5-235">在 [**組建分支篩選**] 區段中，按一下 [**新增**] 下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="b89e5-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="b89e5-236">選擇 [ **組建定義** ] 的 [預設分支] 選項。</span><span class="sxs-lookup"><span data-stu-id="b89e5-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="b89e5-237">此篩選器只會對 GitHub 存放庫預設分支中的組建觸發發行， (*主要*) 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*main*).</span></span>
1. <span data-ttu-id="b89e5-238">按一下 [儲存]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-238">Click the **Save** button.</span></span> <span data-ttu-id="b89e5-239">在產生的 [**儲存** 模式] 對話方塊中，按一下 [**確定]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="b89e5-240">按一下 [ **環境 1** ] 方塊。</span><span class="sxs-lookup"><span data-stu-id="b89e5-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="b89e5-241">**環境** 面板會顯示在右側。</span><span class="sxs-lookup"><span data-stu-id="b89e5-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="b89e5-242">將 [**環境名稱**] 文字方塊中的 *環境 1* 文字變更為 [*生產*]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![發行管線-環境名稱文字方塊](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="b89e5-244">在 [**生產**] 方塊中，按一下 [ **1 個階段]、[2** 個工作] 連結：</span><span class="sxs-lookup"><span data-stu-id="b89e5-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![發行管線-生產環境 link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="b89e5-246">環境的 **[工作]** 索引標籤隨即出現。</span><span class="sxs-lookup"><span data-stu-id="b89e5-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="b89e5-247">按一下 [將 **Azure App Service 部署至插槽** ] 工作。</span><span class="sxs-lookup"><span data-stu-id="b89e5-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="b89e5-248">其設定會出現在右側面板中。</span><span class="sxs-lookup"><span data-stu-id="b89e5-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="b89e5-249">從 [ **azure 訂** 用帳戶] 下拉式清單中，選取與 App Service 相關聯的 azure 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="b89e5-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="b89e5-250">一旦選取之後，請按一下 [ **授權** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="b89e5-251">從 [**應用程式類型**] 下拉式清單中選取 [ *Web 應用程式*]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="b89e5-252">從 [**應用程式服務名稱**] 下拉式清單中，選取 [ *mywebapp]/[<] unique_number/>* 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="b89e5-253">從 [**資源群組**] 下拉式清單中選取 [ *AzureTutorial* ]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="b89e5-254">從 **[位置**] 下拉式清單中選取 [*預備*]。</span><span class="sxs-lookup"><span data-stu-id="b89e5-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="b89e5-255">按一下 [儲存]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="b89e5-256">將滑鼠停留在預設發行管線名稱上方。</span><span class="sxs-lookup"><span data-stu-id="b89e5-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="b89e5-257">按一下鉛筆圖示來編輯它。</span><span class="sxs-lookup"><span data-stu-id="b89e5-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="b89e5-258">使用 *MyFirstProject-ASP.NET Core-CD* 作為名稱。</span><span class="sxs-lookup"><span data-stu-id="b89e5-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![發行管線名稱](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="b89e5-260">按一下 [儲存]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="b89e5-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="b89e5-261">將變更認可至 GitHub 並自動部署至 Azure</span><span class="sxs-lookup"><span data-stu-id="b89e5-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="b89e5-262">在 Visual Studio 中開啟 *SimpleFeedReader .sln* 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="b89e5-263">在 [方案 Explorer] 中，開啟 *Pages\Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="b89e5-264">將 `<h2>Simple Feed Reader - V3</h2>` 變更為 `<h2>Simple Feed Reader - V4</h2>`。</span><span class="sxs-lookup"><span data-stu-id="b89e5-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="b89e5-265">按 **Ctrl** + **Shift** + **B** 來建立應用程式。</span><span class="sxs-lookup"><span data-stu-id="b89e5-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="b89e5-266">將檔案認可至 GitHub 存放庫。</span><span class="sxs-lookup"><span data-stu-id="b89e5-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="b89e5-267">使用 Visual Studio *Team Explorer* 索引標籤中的 [**變更**] 頁面，或使用本機電腦的命令 shell 來執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b89e5-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="b89e5-268">將預設分支 (*主要*) 中的變更推送至 GitHub 存放庫的 *原始* 遠端。</span><span class="sxs-lookup"><span data-stu-id="b89e5-268">Push the change in the default branch (*main*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="b89e5-269">在下列命令中，將預留位置取代 `{BRANCH}` 為預設分支 (使用 `main`) ：</span><span class="sxs-lookup"><span data-stu-id="b89e5-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `main`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="b89e5-270">認可會出現在 GitHub 存放庫的預設分支中 (*主要*) ：</span><span class="sxs-lookup"><span data-stu-id="b89e5-270">The commit appears in the GitHub repository's default branch (*main*):</span></span>

    ![預設分支中的 GitHub 認可 (主要) ](media/cicd/github-commit.png)

    <span data-ttu-id="b89e5-272">因為已在組建定義的 [ **觸發** 程式] 索引標籤中啟用連續整合，所以會觸發組建：</span><span class="sxs-lookup"><span data-stu-id="b89e5-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![啟用持續整合](media/cicd/enable-ci.png)

1. <span data-ttu-id="b89e5-274">流覽至 azure DevOps Services 中 [ **azure 管線** 組建] 頁面的 [已 **佇列**] 索引標籤  >   。</span><span class="sxs-lookup"><span data-stu-id="b89e5-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="b89e5-275">已排入佇列的組建會顯示觸發組建的分支和認可：</span><span class="sxs-lookup"><span data-stu-id="b89e5-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![排入佇列的組建](media/cicd/build-queued.png)

1. <span data-ttu-id="b89e5-277">組建成功之後，即會部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="b89e5-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="b89e5-278">在瀏覽器中流覽至應用程式。</span><span class="sxs-lookup"><span data-stu-id="b89e5-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="b89e5-279">請注意，"V4" 文字會出現在標題中：</span><span class="sxs-lookup"><span data-stu-id="b89e5-279">Notice that the "V4" text appears in the heading:</span></span>

    ![更新的應用程式](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="b89e5-281">檢查 Azure 管線管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="b89e5-282">組建定義</span><span class="sxs-lookup"><span data-stu-id="b89e5-282">Build definition</span></span>

<span data-ttu-id="b89e5-283">組建定義是以 *MyFirstProject-ASP.NET Core-CI* 的名稱建立。</span><span class="sxs-lookup"><span data-stu-id="b89e5-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="b89e5-284">完成時，組建會產生一個 *.zip* 檔案，其中包含要發佈的資產。</span><span class="sxs-lookup"><span data-stu-id="b89e5-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="b89e5-285">發行管線會將這些資產部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="b89e5-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="b89e5-286">組建定義的 [ **工作] 索引** 標籤會列出所使用的個別步驟。</span><span class="sxs-lookup"><span data-stu-id="b89e5-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="b89e5-287">有五個組建工作。</span><span class="sxs-lookup"><span data-stu-id="b89e5-287">There are five build tasks.</span></span>

![組建定義工作](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="b89e5-289">**還原** &mdash; 執行 `dotnet restore` 命令以還原應用程式的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="b89e5-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="b89e5-290">使用的預設套件摘要是 nuget.org。</span><span class="sxs-lookup"><span data-stu-id="b89e5-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="b89e5-291">**組建** &mdash; 執行 `dotnet build --configuration release` 命令以編譯應用程式的程式碼。</span><span class="sxs-lookup"><span data-stu-id="b89e5-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="b89e5-292">此 `--configuration` 選項可用來產生程式碼的優化版本，適用于部署到生產環境。</span><span class="sxs-lookup"><span data-stu-id="b89e5-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="b89e5-293">如果需要 debug 設定，請在組建定義的 [**變數**] 索引標籤上修改 *BuildConfiguration* 變數。</span><span class="sxs-lookup"><span data-stu-id="b89e5-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="b89e5-294">**測試** &mdash; 執行 `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 命令以執行應用程式的單元測試。</span><span class="sxs-lookup"><span data-stu-id="b89e5-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="b89e5-295">單元測試會在符合 glob 模式的任何 c # 專案內執行 `**/*Tests/*.csproj` 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="b89e5-296">測試結果會儲存在選項所指定位置的 *.trx* 檔案中。 `--results-directory`</span><span class="sxs-lookup"><span data-stu-id="b89e5-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="b89e5-297">如果有任何測試失敗，組建就會失敗，而且不會部署。</span><span class="sxs-lookup"><span data-stu-id="b89e5-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b89e5-298">若要確認單元測試是否正常運作，請修改 *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* 以特意中斷其中一個測試。</span><span class="sxs-lookup"><span data-stu-id="b89e5-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="b89e5-299">例如， `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` 在方法中變更為 `Returns_News_Stories_Given_Valid_Uri` 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="b89e5-300">認可變更並推送至 GitHub。</span><span class="sxs-lookup"><span data-stu-id="b89e5-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="b89e5-301">組建會觸發且失敗。</span><span class="sxs-lookup"><span data-stu-id="b89e5-301">The build is triggered and fails.</span></span> <span data-ttu-id="b89e5-302">組建管線狀態變更為「 **失敗**」。</span><span class="sxs-lookup"><span data-stu-id="b89e5-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="b89e5-303">還原變更、認可和推送。</span><span class="sxs-lookup"><span data-stu-id="b89e5-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="b89e5-304">組建成功。</span><span class="sxs-lookup"><span data-stu-id="b89e5-304">The build succeeds.</span></span>

1. <span data-ttu-id="b89e5-305">**發佈** &mdash;執行 `dotnet publish --configuration release --output <local_path_on_build_agent>` 命令以產生包含要部署之成品的 *.zip 檔案。*</span><span class="sxs-lookup"><span data-stu-id="b89e5-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="b89e5-306">`--output`選項會指定 *.zip* 檔案的發佈位置。</span><span class="sxs-lookup"><span data-stu-id="b89e5-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="b89e5-307">您可以傳遞名為的 [預先定義變數](/azure/devops/pipelines/build/variables) 來指定該位置 `$(build.artifactstagingdirectory)` 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="b89e5-308">該變數會展開至組建代理程式上的本機路徑，例如 *c:\agent \_ work\1\a*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="b89e5-309">**發佈成品** &mdash;發佈 **發行** 工作所產生的 *.zip* 檔案。</span><span class="sxs-lookup"><span data-stu-id="b89e5-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="b89e5-310">此工作會接受 *.zip* 檔案的位置做為參數，也就是預先定義的變數 `$(build.artifactstagingdirectory)` 。</span><span class="sxs-lookup"><span data-stu-id="b89e5-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="b89e5-311">*.Zip* 檔案會發佈為名為 *drop* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b89e5-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="b89e5-312">按一下組建定義的 **摘要** 連結，以使用定義來查看組建的歷程記錄：</span><span class="sxs-lookup"><span data-stu-id="b89e5-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![顯示組建定義歷程記錄的螢幕擷取畫面](media/cicd/build-definition-summary.png)

<span data-ttu-id="b89e5-314">在產生的頁面上，按一下對應于唯一組建編號的連結：</span><span class="sxs-lookup"><span data-stu-id="b89e5-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![顯示組建定義摘要頁面的螢幕擷取畫面](media/cicd/build-definition-completed.png)

<span data-ttu-id="b89e5-316">此特定組建的摘要隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="b89e5-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="b89e5-317">按一下 [ **成品] 索引** 標籤，並注意此組建所產生的 *放置* 資料夾如下所示：</span><span class="sxs-lookup"><span data-stu-id="b89e5-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![顯示組建定義構件-drop 資料夾的螢幕擷取畫面](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="b89e5-319">您可以使用 [ **下載** ] 和 [ **流覽** ] 連結來檢查已發佈的成品。</span><span class="sxs-lookup"><span data-stu-id="b89e5-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="b89e5-320">發行管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-320">Release pipeline</span></span>

<span data-ttu-id="b89e5-321">使用名稱 *MyFirstProject-ASP.NET Core-CD* 建立發行管線：</span><span class="sxs-lookup"><span data-stu-id="b89e5-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![顯示發行管線總覽的螢幕擷取畫面](media/cicd/release-definition-overview.png)

<span data-ttu-id="b89e5-323">發行管線的兩個主要元件是 **構件** 和 **環境**。</span><span class="sxs-lookup"><span data-stu-id="b89e5-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="b89e5-324">按一下 [成品 **] 區段中的方塊** ，會顯示下列面板：</span><span class="sxs-lookup"><span data-stu-id="b89e5-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![顯示發行管線構件的螢幕擷取畫面](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="b89e5-326">**來源 (組建定義)** 值表示此發行管線所連結的組建定義。</span><span class="sxs-lookup"><span data-stu-id="b89e5-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="b89e5-327">成功執行組建定義所產生的 *.zip* 檔案會提供給 *生產* 環境，以便部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="b89e5-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="b89e5-328">在 [*生產* 環境] 方塊中，按一下 [ *1 個階段]、[2* 個工作] 連結，以查看發行管線工作：</span><span class="sxs-lookup"><span data-stu-id="b89e5-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![顯示發行管線工作的螢幕擷取畫面](media/cicd/release-definition-tasks.png)

<span data-ttu-id="b89e5-330">發行管線包含兩項工作： *將 Azure App Service 部署至插槽* ，以及 *管理 azure app Service-位置交換*。</span><span class="sxs-lookup"><span data-stu-id="b89e5-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="b89e5-331">按一下第一項工作會顯示下列工作設定：</span><span class="sxs-lookup"><span data-stu-id="b89e5-331">Clicking the first task reveals the following task configuration:</span></span>

![顯示發行管線部署工作的螢幕擷取畫面](media/cicd/release-definition-task1.png)

<span data-ttu-id="b89e5-333">Azure 訂用帳戶、服務類型、web 應用程式名稱、資源群組和部署位置會在部署工作中定義。</span><span class="sxs-lookup"><span data-stu-id="b89e5-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="b89e5-334">[**套件或資料夾**] 文字方塊會保存要解壓縮並部署至 *mywebapp \<unique_number\>* web 應用程式 *預備* 位置的 .zip 檔案路徑 *。*</span><span class="sxs-lookup"><span data-stu-id="b89e5-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="b89e5-335">按一下位置交換工作會顯示下列工作設定：</span><span class="sxs-lookup"><span data-stu-id="b89e5-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![顯示發行管線位置交換工作的螢幕擷取畫面](media/cicd/release-definition-task2.png)

<span data-ttu-id="b89e5-337">系統會提供訂用帳戶、資源群組、服務類型、web 應用程式名稱和部署位置詳細資料。</span><span class="sxs-lookup"><span data-stu-id="b89e5-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="b89e5-338">核取 [ **與生產環境交換** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="b89e5-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="b89e5-339">因此，部署至 *預備* 位置的位會交換至生產環境。</span><span class="sxs-lookup"><span data-stu-id="b89e5-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="b89e5-340">延伸閱讀</span><span class="sxs-lookup"><span data-stu-id="b89e5-340">Additional reading</span></span>

* [<span data-ttu-id="b89e5-341">使用 Azure Pipelines 建立您的第一個管線</span><span class="sxs-lookup"><span data-stu-id="b89e5-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="b89e5-342">組建和 .NET Core 專案</span><span class="sxs-lookup"><span data-stu-id="b89e5-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="b89e5-343">使用 Azure 管線部署 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="b89e5-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
