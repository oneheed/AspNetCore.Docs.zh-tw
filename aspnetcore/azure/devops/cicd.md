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
# <a name="continuous-integration-and-deployment"></a>持續整合與部署

在上一章中，您已建立簡單摘要讀取器應用程式的本機 Git 存放庫。 在本章中，您會將該程式碼發佈至 GitHub 存放庫，並使用 Azure 管線來建立 Azure DevOps Services 管線。 管線可讓應用程式持續組建和部署。 任何對 GitHub 存放庫的認可都會觸發組建，以及部署至 Azure Web 應用程式的預備位置。

在本節中，您將完成下列工作：

* 將應用程式的程式碼發佈至 GitHub
* 中斷本機 Git 部署的連線
* 建立 Azure DevOps 組織
* 在 Azure DevOps Services 中建立 team 專案
* 建立組建定義
* 建立發行管線
* 將變更認可至 GitHub 並自動部署至 Azure
* 檢查 Azure 管線管線

## <a name="publish-the-apps-code-to-github"></a>將應用程式的程式碼發佈至 GitHub

1. 開啟瀏覽器視窗，並流覽至 `https://github.com` 。
1. 按一下 **+** 標頭中的下拉式清單，然後選取 [ **新增存放庫**]：

    ![GitHub 新的存放庫選項](media/cicd/github-new-repo.png)

1. 在 [**擁有** 者] 下拉式清單中選取您的帳戶，然後在 [存放 **庫名稱**] 文字方塊中輸入 *簡單摘要讀取程式*。
1. 按一下 [ **建立存放庫** ] 按鈕。
1. 開啟您本機電腦的命令 shell。 流覽至儲存 *簡單摘要讀取器* Git 儲存機制的目錄。
1. 將現有的 *來源* 遠端重新命名為 *上游*。 執行以下命令：

    ```console
    git remote rename origin upstream
    ```

1. 新增指向 GitHub 上存放庫複本的新 *原始來源* 遠端。 執行以下命令：

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. 將您的本機 Git 存放庫發佈到新建立的 GitHub 存放庫。 執行以下命令：

    ```console
    git push -u origin main
    ```

1. 開啟瀏覽器視窗，並流覽至 `https://github.com/<GitHub_username>/simple-feed-reader/` 。 驗證您的程式碼出現在 GitHub 存放庫中。

## <a name="disconnect-local-git-deployment"></a>中斷本機 Git 部署的連線

使用下列步驟移除本機 Git 部署。  (Azure DevOps 服務的 azure 管線) 都取代並擴充該功能。

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後流覽至 *預備環境 (mywebapp \<unique_number\> /Staging)* Web 應用程式。 在入口網站的 [搜尋] 方塊中輸入 *預備* 環境，即可快速找出 Web 應用程式：

    ![預備 Web 應用程式搜尋詞彙](media/cicd/portal-search-box.png)

1. 按一下 [ **部署中心**]。 新的面板隨即出現。 按一下 **[中斷連線]** ，移除上一章中新增的本機 Git 原始檔控制設定。 按一下 [ **是]** 按鈕，確認移除操作。
1. 流覽至 *mywebapp<unique_number>* App Service。 提醒您，入口網站的搜尋方塊可以用來快速找出 App Service。
1. 按一下 [ **部署中心**]。 新的面板隨即出現。 按一下 **[中斷連線]** ，移除上一章中新增的本機 Git 原始檔控制設定。 按一下 [ **是]** 按鈕，確認移除操作。

## <a name="create-an-azure-devops-organization"></a>建立 Azure DevOps 組織

1. 開啟瀏覽器，然後流覽至 [ [Azure DevOps 組織建立] 頁面](https://go.microsoft.com/fwlink/?LinkId=307137)。
1. 在 [ **挑選易記名稱** ] 文字方塊中輸入唯一的名稱，以形成用來存取 Azure DevOps 組織的 URL。
1. 選取 **Git** 選項按鈕，因為程式碼裝載于 GitHub 存放庫中。
1. 按一下 [繼續] 按鈕。 短暫等候之後，就會建立名為 *MyFirstProject* 的帳戶和 team 專案。

    ![Azure DevOps 組織建立頁面](media/cicd/vsts-account-creation.png)

1. 開啟確認電子郵件，指出 Azure DevOps 組織和專案已準備好可供使用。 按一下 [ **啟動您的專案** ] 按鈕：

    ![啟動專案按鈕](media/cicd/vsts-start-project.png)

1. 瀏覽器會開啟 *\<account_name\> visualstudio.com*。 按一下 [ *MyFirstProject* ] 連結，開始設定專案的 DevOps 管線。

## <a name="configure-the-azure-pipelines-pipeline"></a>設定 Azure 管線管線

完成三個不同的步驟。 完成下列三個區段中的步驟，會導致操作 DevOps 管線。

### <a name="grant-azure-devops-access-to-the-github-repository"></a>將 GitHub 存放庫的存取權授與 Azure DevOps

1. 從外部存放庫的 [可折疊] 展開 **或建立程式碼** 。 按一下 [ **安裝組建** ] 按鈕：

    ![設定組建按鈕](media/cicd/vsts-setup-build.png)

1. 從 [**選取來源**] 區段中選取 **GitHub** 選項：

    ![選取來源-GitHub](media/cicd/vsts-select-source.png)

1. 需要授權，Azure DevOps 才能存取您的 GitHub 存放庫。 在 [連線 **名稱**] 文字方塊中，輸入 *<GitHub_username> GitHub 連接*。 例如：

    ![GitHub 連接名稱](media/cicd/vsts-repo-authz.png)

1. 如果您的 GitHub 帳戶已啟用雙因素驗證，則需要個人存取權杖。 在此情況下，請按一下 [ **使用 GitHub 個人存取權杖的授權** ] 連結。 如需協助，請參閱 [官方 GitHub 個人存取權杖的建立指示](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) 。 只需要許可權的存放 *庫範圍。* 否則，請按一下 [ **使用 OAuth 授權** ] 按鈕。
1. 出現提示時，請登入您的 GitHub 帳戶。 然後選取 [授權]，以授與 Azure DevOps 組織的存取權。 如果成功，就會建立新的服務端點。
1. 按一下 [存放 **庫** ] 按鈕旁的省略號按鈕。 從清單中選取 *<GitHub_username>/simple-feed-reader* 存放庫。 按一下 [選取] 按鈕。
1. 從 [**手動和排程的組建**] 下拉式清單中，選取預設分支 (*主要*) 。 按一下 [繼續] 按鈕。 [範本選取] 頁面隨即出現。

### <a name="create-the-build-definition"></a>建立組建定義

1. 在 [範本選擇] 頁面的 [搜尋] 方塊中，輸入 *ASP.NET Core* ：

    ![在範本頁面上 ASP.NET 核心搜尋](media/cicd/vsts-template-selection.png)

1. 範本搜尋結果隨即出現。 將滑鼠停留在 **ASP.NET Core** 範本上方，然後按一下 [套用 **] 按鈕。**
1. 組建 **定義的 [工作]** 索引標籤隨即出現。 按一下 [ **觸發** 程式] 索引標籤。
1. 勾選 [ **啟用持續整合** ] 方塊。 在 [ **分支篩選** ] 區段下，確認 [ **類型** ] 下拉式清單已設定為 [ *包含*]。 將 [ **分支規格** ] 下拉式清單設定為 [ *主要*]。

    ![啟用持續整合設定](media/cicd/vsts-enable-ci.png)

    這些設定會在將任何變更推送至 GitHub 存放庫 (*主要*) 的預設分支時，造成組建觸發程式。 持續整合會在將 [變更認可至 GitHub 並自動部署至 Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) 區段中進行測試。

1. 按一下 [ **儲存 & 佇列** ] 按鈕，然後選取 [ **儲存** ] 選項：

    ![[儲存] 按鈕](media/cicd/vsts-save-build.png)

1. 下列強制回應對話方塊隨即出現：

    ![儲存組建定義-強制回應對話方塊](media/cicd/vsts-save-modal.png)

    使用的預設資料夾 *\\* ，然後按一下 [ **儲存** ] 按鈕。

### <a name="create-the-release-pipeline"></a>建立發行管線

1. 按一下 team 專案的 [ **發行** ] 索引標籤。 按一下 [ **新增管線** ] 按鈕。

    ![[發行] 索引標籤-[新增定義] 按鈕](media/cicd/vsts-new-release-definition.png)

    [範本選取範圍] 窗格隨即出現。

1. 在 [範本選擇] 頁面的 [搜尋] 方塊中，輸入 *App Service* ：

    ![發行管線範本搜尋方塊](media/cicd/vsts-release-template-search.png)

1. 範本搜尋結果隨即出現。 將滑鼠停留在具有位置範本的 **Azure App Service 部署** 上，然後按一下 [套用 **] 按鈕。** 發行管線的 [ **管線** ] 索引標籤隨即出現。

    ![發行管線管線索引標籤](media/cicd/vsts-release-definition-pipeline.png)

1. 按一下 [成品] 方塊中 **的 [** **加入**] 按鈕。 [ **新增** 成品] 面板隨即出現：

    ![發行管線-新增成品面板](media/cicd/vsts-release-add-artifact.png)

1. 從 [**來源類型**] 區段中選取 [**組建**] 磚。 此類型可讓您將發行管線連結至組建定義。
1. 從 [**專案**] 下拉式清單中選取 [ *MyFirstProject* ]。
1. 從 [**來源 (組建定義])** 下拉式清單中，選取組建定義名稱 *MYFIRSTPROJECT-ASP.NET Core-CI*。
1. 從 **預設版本** 下拉式清單中選取 [*最新*]。 此選項會建立最新的組建定義執行所產生的構件。
1. 以 *Drop* 取代 [**來源別名**] 文字方塊中的文字。
1. 按一下 [新增]  按鈕。 [ **構件** ] 區段會更新以顯示變更。
1. 按一下閃電圖示，以啟用持續部署：

    ![發行管線構件-閃電圖示](media/cicd/vsts-artifacts-lightning-bolt.png)

    啟用此選項後，每次有新的組建時，就會進行部署。
1. [ **持續部署觸發** 程式] 面板會顯示在右側。 按一下切換按鈕以啟用此功能。 不需要啟用 **提取要求觸發** 程式。
1. 在 [**組建分支篩選**] 區段中，按一下 [**新增**] 下拉式清單。 選擇 [ **組建定義** ] 的 [預設分支] 選項。 此篩選器只會對 GitHub 存放庫預設分支中的組建觸發發行， (*主要*) 。
1. 按一下 [儲存]  按鈕。 在產生的 [**儲存** 模式] 對話方塊中，按一下 [**確定]** 按鈕。
1. 按一下 [ **環境 1** ] 方塊。 **環境** 面板會顯示在右側。 將 [**環境名稱**] 文字方塊中的 *環境 1* 文字變更為 [*生產*]。

   ![發行管線-環境名稱文字方塊](media/cicd/vsts-environment-name-textbox.png)

1. 在 [**生產**] 方塊中，按一下 [ **1 個階段]、[2** 個工作] 連結：

    ![發行管線-生產環境 link.png](media/cicd/vsts-production-link.png)

    環境的 **[工作]** 索引標籤隨即出現。
1. 按一下 [將 **Azure App Service 部署至插槽** ] 工作。 其設定會出現在右側面板中。
1. 從 [ **azure 訂** 用帳戶] 下拉式清單中，選取與 App Service 相關聯的 azure 訂用帳戶。 一旦選取之後，請按一下 [ **授權** ] 按鈕。
1. 從 [**應用程式類型**] 下拉式清單中選取 [ *Web 應用程式*]。
1. 從 [**應用程式服務名稱**] 下拉式清單中，選取 [ *mywebapp]/[<] unique_number/>* 。
1. 從 [**資源群組**] 下拉式清單中選取 [ *AzureTutorial* ]。
1. 從 **[位置**] 下拉式清單中選取 [*預備*]。
1. 按一下 [儲存]  按鈕。
1. 將滑鼠停留在預設發行管線名稱上方。 按一下鉛筆圖示來編輯它。 使用 *MyFirstProject-ASP.NET Core-CD* 作為名稱。

    ![發行管線名稱](media/cicd/vsts-release-definition-name.png)

1. 按一下 [儲存]  按鈕。

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>將變更認可至 GitHub 並自動部署至 Azure

1. 在 Visual Studio 中開啟 *SimpleFeedReader .sln* 。
1. 在 [方案 Explorer] 中，開啟 *Pages\Index.cshtml*。 將 `<h2>Simple Feed Reader - V3</h2>` 變更為 `<h2>Simple Feed Reader - V4</h2>`。
1. 按 **Ctrl** + **Shift** + **B** 來建立應用程式。
1. 將檔案認可至 GitHub 存放庫。 使用 Visual Studio *Team Explorer* 索引標籤中的 [**變更**] 頁面，或使用本機電腦的命令 shell 來執行下列命令：

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. 將預設分支 (*主要*) 中的變更推送至 GitHub 存放庫的 *原始* 遠端。 在下列命令中，將預留位置取代 `{BRANCH}` 為預設分支 (使用 `main`) ：

    ```console
    git push origin {BRANCH}
    ```

    認可會出現在 GitHub 存放庫的預設分支中 (*主要*) ：

    ![預設分支中的 GitHub 認可 (主要) ](media/cicd/github-commit.png)

    因為已在組建定義的 [ **觸發** 程式] 索引標籤中啟用連續整合，所以會觸發組建：

    ![啟用持續整合](media/cicd/enable-ci.png)

1. 流覽至 azure DevOps Services 中 [ **azure 管線** 組建] 頁面的 [已 **佇列**] 索引標籤  >   。 已排入佇列的組建會顯示觸發組建的分支和認可：

    ![排入佇列的組建](media/cicd/build-queued.png)

1. 組建成功之後，即會部署至 Azure。 在瀏覽器中流覽至應用程式。 請注意，"V4" 文字會出現在標題中：

    ![更新的應用程式](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>檢查 Azure 管線管線

### <a name="build-definition"></a>組建定義

組建定義是以 *MyFirstProject-ASP.NET Core-CI* 的名稱建立。 完成時，組建會產生一個 *.zip* 檔案，其中包含要發佈的資產。 發行管線會將這些資產部署至 Azure。

組建定義的 [ **工作] 索引** 標籤會列出所使用的個別步驟。 有五個組建工作。

![組建定義工作](media/cicd/build-definition-tasks.png)

1. **還原** &mdash; 執行 `dotnet restore` 命令以還原應用程式的 NuGet 套件。 使用的預設套件摘要是 nuget.org。
1. **組建** &mdash; 執行 `dotnet build --configuration release` 命令以編譯應用程式的程式碼。 此 `--configuration` 選項可用來產生程式碼的優化版本，適用于部署到生產環境。 如果需要 debug 設定，請在組建定義的 [**變數**] 索引標籤上修改 *BuildConfiguration* 變數。
1. **測試** &mdash; 執行 `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 命令以執行應用程式的單元測試。 單元測試會在符合 glob 模式的任何 c # 專案內執行 `**/*Tests/*.csproj` 。 測試結果會儲存在選項所指定位置的 *.trx* 檔案中。 `--results-directory` 如果有任何測試失敗，組建就會失敗，而且不會部署。

    > [!NOTE]
    > 若要確認單元測試是否正常運作，請修改 *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* 以特意中斷其中一個測試。 例如， `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` 在方法中變更為 `Returns_News_Stories_Given_Valid_Uri` 。 認可變更並推送至 GitHub。 組建會觸發且失敗。 組建管線狀態變更為「 **失敗**」。 還原變更、認可和推送。 組建成功。

1. **發佈** &mdash;執行 `dotnet publish --configuration release --output <local_path_on_build_agent>` 命令以產生包含要部署之成品的 *.zip 檔案。* `--output`選項會指定 *.zip* 檔案的發佈位置。 您可以傳遞名為的 [預先定義變數](/azure/devops/pipelines/build/variables) 來指定該位置 `$(build.artifactstagingdirectory)` 。 該變數會展開至組建代理程式上的本機路徑，例如 *c:\agent \_ work\1\a*。
1. **發佈成品** &mdash;發佈 **發行** 工作所產生的 *.zip* 檔案。 此工作會接受 *.zip* 檔案的位置做為參數，也就是預先定義的變數 `$(build.artifactstagingdirectory)` 。 *.Zip* 檔案會發佈為名為 *drop* 的資料夾。

按一下組建定義的 **摘要** 連結，以使用定義來查看組建的歷程記錄：

![顯示組建定義歷程記錄的螢幕擷取畫面](media/cicd/build-definition-summary.png)

在產生的頁面上，按一下對應于唯一組建編號的連結：

![顯示組建定義摘要頁面的螢幕擷取畫面](media/cicd/build-definition-completed.png)

此特定組建的摘要隨即顯示。 按一下 [ **成品] 索引** 標籤，並注意此組建所產生的 *放置* 資料夾如下所示：

![顯示組建定義構件-drop 資料夾的螢幕擷取畫面](media/cicd/build-definition-artifacts.png)

您可以使用 [ **下載** ] 和 [ **流覽** ] 連結來檢查已發佈的成品。

### <a name="release-pipeline"></a>發行管線

使用名稱 *MyFirstProject-ASP.NET Core-CD* 建立發行管線：

![顯示發行管線總覽的螢幕擷取畫面](media/cicd/release-definition-overview.png)

發行管線的兩個主要元件是 **構件** 和 **環境**。 按一下 [成品 **] 區段中的方塊** ，會顯示下列面板：

![顯示發行管線構件的螢幕擷取畫面](media/cicd/release-definition-artifacts.png)

**來源 (組建定義)** 值表示此發行管線所連結的組建定義。 成功執行組建定義所產生的 *.zip* 檔案會提供給 *生產* 環境，以便部署到 Azure。 在 [*生產* 環境] 方塊中，按一下 [ *1 個階段]、[2* 個工作] 連結，以查看發行管線工作：

![顯示發行管線工作的螢幕擷取畫面](media/cicd/release-definition-tasks.png)

發行管線包含兩項工作： *將 Azure App Service 部署至插槽* ，以及 *管理 azure app Service-位置交換*。 按一下第一項工作會顯示下列工作設定：

![顯示發行管線部署工作的螢幕擷取畫面](media/cicd/release-definition-task1.png)

Azure 訂用帳戶、服務類型、web 應用程式名稱、資源群組和部署位置會在部署工作中定義。 [**套件或資料夾**] 文字方塊會保存要解壓縮並部署至 *mywebapp \<unique_number\>* web 應用程式 *預備* 位置的 .zip 檔案路徑 *。*

按一下位置交換工作會顯示下列工作設定：

![顯示發行管線位置交換工作的螢幕擷取畫面](media/cicd/release-definition-task2.png)

系統會提供訂用帳戶、資源群組、服務類型、web 應用程式名稱和部署位置詳細資料。 核取 [ **與生產環境交換** ] 核取方塊。 因此，部署至 *預備* 位置的位會交換至生產環境。

## <a name="additional-reading"></a>延伸閱讀

* [使用 Azure Pipelines 建立您的第一個管線](/azure/devops/pipelines/get-started-yaml)
* [組建和 .NET Core 專案](/azure/devops/pipelines/languages/dotnet-core)
* [使用 Azure 管線部署 web 應用程式](/azure/devops/pipelines/targets/webapp)
